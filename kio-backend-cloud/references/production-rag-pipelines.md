# Production RAG & Re-ranking Architecture

## 1. Reciprocal Rank Fusion (RRF) for Hybrid Search
Combine sparse (BM25) and dense (Vector) rankings using RRF:
```python
def reciprocal_rank_fusion(dense_ranks, sparse_ranks, k=60):
    rrf_score = {}
    for rank, doc_id in enumerate(dense_ranks):
        rrf_score[doc_id] = rrf_score.get(doc_id, 0) + 1.0 / (k + rank + 1)
    for rank, doc_id in enumerate(sparse_ranks):
        rrf_score[doc_id] = rrf_score.get(doc_id, 0) + 1.0 / (k + rank + 1)
    return sorted(rrf_score.items(), key=lambda x: x[1], reverse=True)
```

## 2. Cross-Encoder Re-Ranking Pipeline
```python
from sentence_transformers import CrossEncoder

reranker = CrossEncoder('BAAI/bge-reranker-large')

query = "How to configure zero downtime deployment in Terraform?"
candidates = ["Doc 1 text...", "Doc 2 text...", "Doc 3 text..."]

pairs = [[query, doc] for doc in candidates]
scores = reranker.predict(pairs)

# Sort candidates by descending relevance score
ranked_docs = [doc for _, doc in sorted(zip(scores, candidates), reverse=True)]
```

## 3. Pipeline overview
```
ingest → clean → chunk → embed → store (vector + BM25) ─┐
query → rewrite (optional) → hybrid retrieve (top 50) → RRF → re-rank (top 5–8) → generate with citations → evaluate
```

## 4. Chunking
- Split on **structure first** (headings, sections, code blocks, table rows), then by size: ~300–800 tokens with 10–15% overlap is a sane default; tune with your eval set.
- Prepend context to each chunk before embedding: document title + section path (`Billing > Refunds > VNPay`). This "contextual header" lifts retrieval a lot for short chunks.
- Store metadata with every chunk: `doc_id`, `source_url`, `section`, `updated_at`, `tenant_id`, `acl`. Filter on it at query time — never rely on the LLM to ignore other tenants' text.
- Re-embed on change: keep a content hash per chunk and only re-embed changed ones.

## 5. Hybrid search in PostgreSQL (pgvector + full-text)
```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE TABLE chunks (
  id        bigserial PRIMARY KEY,
  tenant_id uuid NOT NULL,
  doc_id    uuid NOT NULL,
  content   text NOT NULL,
  embedding vector(1024) NOT NULL,
  tsv       tsvector GENERATED ALWAYS AS (to_tsvector('simple', content)) STORED
);
CREATE INDEX ON chunks USING hnsw (embedding vector_cosine_ops);
CREATE INDEX ON chunks USING GIN (tsv);
CREATE INDEX ON chunks (tenant_id);

-- Dense candidates
SELECT id FROM chunks WHERE tenant_id = $1 ORDER BY embedding <=> $2 LIMIT 50;
-- Sparse candidates
SELECT id FROM chunks WHERE tenant_id = $1 AND tsv @@ websearch_to_tsquery('simple', $3)
ORDER BY ts_rank(tsv, websearch_to_tsquery('simple', $3)) DESC LIMIT 50;
```
Fuse the two ID lists with RRF (§1), then re-rank (§2). Use the `'simple'` config (or `unaccent`) for Vietnamese; language stemmers built for English hurt it.

## 6. Generation rules
- Put retrieved chunks in clearly delimited blocks with IDs; require the answer to **cite chunk IDs** and to say "not found in the provided sources" when they do not contain the answer.
- Treat retrieved text as **data, not instructions** (prompt-injection defence): never let a document change the system prompt or call tools on its own.
- Cap context: 5–8 re-ranked chunks usually beat 30 raw ones on accuracy, latency and cost.

## 7. Operating it
- Log query, retrieved IDs, re-rank scores, answer and user feedback; this is your next eval set.
- Cache embeddings of frequent queries; batch embedding calls at ingest.
- Track retrieval quality (recall@k on the eval set) separately from answer quality — most "LLM errors" are retrieval misses.
