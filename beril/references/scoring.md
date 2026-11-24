# Scoring and ranking

Beril collects more than one person can read. This file decides what survives.

## The five scores

Score each item 0 to 5 on five axes. The maximum is 25.

| Axis | 0 | 3 | 5 |
|---|---|---|---|
| Relevance | Off topic | Useful background | Directly answers the scope question |
| Novelty | Known for years | Fresh angle on a known issue | New primitive or new target class |
| Proof | Opinion | Code or request but no result | Reproducible evidence with a result |
| Transferability | One target only | Same stack or product family | Applies across many targets |
| Freshness | Older than 3 years | 1 to 3 years | Last 12 months |

Keep items at 18 or higher. Read items from 14 to 17 when the layer is thin. Drop anything below 14 unless it is the only source.

## Weights by layer

The axes matter differently per layer.

| Layer | Most important | Least important |
|---|---|---|
| Threads and reports | Relevance, proof | Freshness |
| Experts | Relevance, transferability | Novelty |
| Tools | Relevance, freshness | Novelty |
| Methods | Proof, transferability | Freshness |

A 2018 writeup with a full request and a working exploit can beat a 2026 news post. The score reflects that.

## Source grades

| Grade | Definition | Use |
|---|---|---|
| A | Primary and reproducible | Can support a finding |
| B | Credible secondary, detailed | Can support a method card with a second source |
| C | Lead or opinion | Starts a search, does not close one |

Rules:

- One A source is enough for a technique claim.
- Two independent B sources are enough if they agree on the mechanism.
- A single C source never closes a claim.
- A vendor advisory plus a researcher writeup is an A/A pair. Use both.

## Duplicate handling

The same work appears in many places: a talk, a blog post, a HackerOne report, and a tweet.

Deduplicate by:

1. Canonical URL. Strip tracking parameters and fragments.
2. Title similarity. Normalize case and punctuation before comparing.
3. Author plus date. Two posts by the same author on the same day are often the same content.
4. Finding identity. The same request and response counts once, even if three people reported it.

Keep the earliest primary source. Add later sources as corroboration, not as separate rows.

## Ranking the reading queue

Sort by:

1. Score, descending.
2. Source grade, A before B before C.
3. Recency, newer first.
4. Reading time, shorter first when scores tie.

Cap the queue at ten items per session. A queue you cannot finish is a backlog, and backlogs rot.

## When to drop an item

Drop it when:

- The link is dead and no archive exists.
- The content is a summary of a primary source you already have.
- The technique depends on a version that is no longer in scope.
- The post is a product announcement with no method.
- The only claim is "experts say" with no named expert.

Do not drop an old item only because it is old. Drop it when the mechanism no longer applies.

## Calibration

Once a month, rescore five items you already read. If the new scores differ by more than five points, your rubric drifted. Write down why.

A scoring system is a tool for attention, not a measure of truth. When a low-scoring item keeps appearing in good reports, read it and adjust the rubric.
