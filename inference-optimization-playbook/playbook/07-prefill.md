# Prefill and re-reading the prompt

Covers DeepSeek V4.1 Flash, GLM-5.3-Flash, MiniMax-M3.1-Flash, Qwen3.8-Flash-Next, Hy4 Flash.

> **Read when:** reading the prompt is slow, on a cold input or on every turn. **Skip when:** the time goes to generation. **Ledger tags:** `#prefill` `#reread` `#long-context`.

"Re-read" here means prefill of the new tokens on top of an already processed context. In agent work it is the common case ([11](11-sessions-and-prompt-cache.md)).

## What limits prefill under offload

On every prefill chunk almost all experts of a layer are needed: 1,300 rows touch 70-80% of the experts in RAM. So on every chunk all the cold experts of the layer travel over PCIe again. A model for DeepSeek: a chunk of N rows touches a cold expert with probability `1 - exp(-6·N·p)` (matches the log within ±15%).

The levers that follow:

| Lever | Example | Effect |
|---|---|---|
| DMA straight from pinned copies instead of memcpy through a staging buffer | DeepSeek, Maya: stock had DMA only for MXFP4, other formats went through staging at ~2.7 GB/s per card | stock → our build (DMA for all formats + skipping layers not needed for the prompt + auto chunk): prefill 32K 430 → 2,199 tok/s |
| Large chunks (move cold experts less often) | GLM fork: chunk 2048 → 4096 gave 764 → 1,000 tok/s. DeepSeek: 8192 with borrowing expert slots for buffers | DeepSeek 128K +15%, 32K -7% (short prompts lose) |
| Borrowing expert VRAM for prefill buffers | DeepSeek: without it 7-8 GB per rig would idle in buffers | see above |
| RAM copies of borrowed experts instead of re-reading from the file | return of the tail 331-516 ms → 82-117 ms per turn | turns -14.7% together with incremental prompt prep ([11](11-sessions-and-prompt-cache.md)) |
| EP: several PCIe links in parallel | DeepSeek: re-read of 1.5K -42% | see below |
| Fused prefill expert kernels with grouping on the GPU | Strata `STRATA_PF_FUSED=1`: prefill 90K +13%, copy/wait 20% → 5% | but KL 0.033 vs stock, not taken into the service |
| Full-speed tensor kernels with an fp32 accumulator on GeForce (gpu-unlock module) | Strata prefill +10-12% (the forecast was +14-19%) | decode unchanged, output bit for bit |
| Experts with 1-N rows computed on the CPU inside a chunk | re-read -20 to -32% | rejected: quality did not pass (CPU rounding) |

## EP in prefill: how it works

Cards in layer split do prefill as a relay: at any moment 1 card computes and pulls data over 1 link. On average the links are busy ~30%. EP uses the idle links:

- while the owner computes its layers, 3 helper cards pull part of its cold experts from RAM, the light ones (with ≤ 16 rows), get the input rows over P2P, compute and return the result;
- the owner keeps the most loaded experts: most of the bytes but few rows;
- the P2P exchange is cheap: on 1.5K it is 1.32 GB vs 47.9 GB of helper DMA (2.8%);
- the ceiling is ~21 GB/s total, and the owner waits for helpers 29-30% of the time.

Result: re-read of 1.5K -42%, 4K -9 to -13%, 8K -6 to -10%. Short re-reads gain more because without EP they were 1 chunk on 1 link. The enable threshold mattered: EP turned on from 64 tokens, and a turn of 40 tokens read longer than one of 197. Lowering the threshold to 16 saved another 0.33 s on every short turn.

Trap: EP buffers are allocated on the first turn, and with little spare VRAM `cudaMalloc` fails and **EP turns off silently**. After launch, check in the log that EP is working.

## The floor of a re-read

On DeepSeek the last number for a 1.5K re-read is 2.9 s. The floor by calculation is 1.6-2.2 s: DMA ~1.2 s (5,700 experts, 63 GB at 52 GB/s) plus the serial part of the layer before MoE ~1.05 s (~26 ms per layer), during which all 4 links idle. Without PCIe 4.0 or without overlapping "routing of the next layer / DMA of the current one" there is no way further.

A non-obvious finding: on MiniMax at long context (after the 32K ring) the main brake of prefill was attention, not expert delivery. The attention kernel read K/V straight from mapped host RAM in 32-byte pieces, the same blocks 73-383 times per chunk. Gathering K/V into VRAM with 1 copy per chunk plus an LRU pool in decode gave prefill 64K ×3.3, and the full session replay ×3.75 (4,050 → 1,084 s, 1 run, not rechecked). Short prefill on MiniMax is limited by expert delivery over 1 link. **Profile before you decide what limits prefill.**

## Big block, small tile (Hy4 Flash, 1 GPU)

**Symptom.** On 1 card only 15% of experts fit in VRAM permanently in the early profile. Weights for a long input crossed PCIe many times. In the trace of the original build, 66.0 GB moved on a 4,096-token segment. In the new build, 67.7 GB on 32,768 tokens. The segments started at different depths, but the gap in bytes per token explains why weight loading was the right thing to work on.

**What changed.** One loaded set of expert weights serves a logical block of up to 57,600 tokens. Attention and dense ops inside it run in tiles of 2,048. SWA head groups are processed separately, DSA uses the positions chosen by the indexer, the causal mask and attention sinks are kept. For DSA: a half-precision representation of KV rows, separate computation of attention scores, softmax and the multiply by V. Expert copies overlap with GPU work: in the new profile, of 5.20 s of copying 4.72 s coincided with kernel execution. The speedup below is for the full build.

| 1 GPU, fresh input | Prefill before → after, tok/s | Decode before → after, tok/s |
|---|---:|---:|
| 83 tokens, code | 28.9 → 35.1 | 39.58 → 44.34 |
| 49,152 tokens, other code | 428.2 → 1,515.5 | 35.12 → 38.21 |
| 57,587 tokens | 415.6 → 1,510.2 | 39.69 → 53.70 |
| 261,888 tokens and 256 positions for the check | 420.0 → 1,422.5 | checked a given continuation |

On the 57,587 input the prefill wait went from 138.55 to 38.13 s. On a separate continuous pass the last segment 230,400-262,016 gave 1,295.8 tok/s. Keep the first short request in the table when you judge interactive mode: it shows buffer creation and graph preparation.

**What to check on another model.** Separate 2 parameters: how many tokens use 1 loaded matrix, and how many activations are materialized at once. First sweep several block sizes at a fixed tile, recording bytes per token, peak VRAM and time outside the overlap.

On 2 cards, 1 double staging buffer with a separate stream lifted prefill from 770 to 846 tok/s at block 2,048. Blocks 4,096 and 8,192 gave 1,225 and 1,588 tok/s, but temporary memory grew from 629 to 1,087 and 2,005 MiB **per card**. A larger block wins until it evicts too many experts for the next phase.

Implementation notes: [project-maya docs/HY4-FLASH.md at 82a77bf](https://github.com/alesha-pro/project-maya/blob/82a77bf/docs/HY4-FLASH.md).

## Attention, projections and graphs (Hy4 Flash, 4 GPUs)

**What worked.** For batch prefill: tiled attention, a long-context indexer parallelized by position, reuse of projections and KV data. In later builds queries and projections were packed for exchange, and a local KV replica at suitable lengths let DSA heads work next to their data. The combination of packing, local heads, overlap and a fast CPU sampler lifted decode on 6 equal tasks from 114.06 to 179.56 tok/s. The contribution of each part cannot be recovered from this ratio.

**Where the gain ends.** Raising the boundary of the local KV replica from 65,536 to 67,072 lifted decode on 1 Russian request from 152.29 to 155.81 tok/s after a cold feed. At depth 261,632, replicating 5 more layers saved only 1.25-1.53% of warmed verify and increased the preparation of the first step from 41.9 to 113.5 ms. In another branch, sending packed query heads to 3 neighbors increased verify T=3 from 12.12 to 13.26 ms.

**How to transfer.** Profile on short and on deep context, then check points on both sides of the boundary of a specialized kernel. When KV is reused, rebuild replicas from the canonical state and test rewind separately.

For short shapes it pays to capture a CUDA Graph at known widths. On Hy4, preparing 3 graphs before the server reports ready lifted the first answer at 98K from 154.83 to 169.60 tok/s in a separate comparison. A variant that kept additional executable graphs alive left too little VRAM for the first prefill. Service start cost and memory reserve are part of the decision.

CUDA graphs in decode under offload on DeepSeek stayed an estimate: +2-3% for days of work, because gaps under 20 µs were only 3.5% of the time (calc).

## Computations removed from prefill (Hy4 Flash)

| Trick | Result or check | Limit of applicability |
|---|---|---|
| Compute only the drafter state that is used | In the MTP branch, while reading the input, only the KV update needed to start proposing tokens was kept. Intermediate attention and FFN outputs not needed by the next step were skipped where the dependency allowed | Compare the first and the following draft positions, especially after a prefix restore |
| Send group IDs instead of an expanded list | Instead of 2,048 int32 indices per request, up to 511 uint16 IDs were sent and the original order was rebuilt on the receiving side | Count the cost of the local rebuild. Check empty groups, repeated assignments and element order |
| Pick the matmul tile by the real fill of experts | On 128 experts with 67 rows on average, tile 64 cut gate/up time on Q3 from 2,202 to 1,989 µs and on Q4 from 2,082 to 1,825 µs. Checked on 13,169,664 FP32 values per type | Collect a histogram of rows per expert on your requests. This is operation time |
| Faster exact top-k before sparse attention | A sampled threshold with a fallback to radix sort cut the time of 64 rows at depth 253,952 from 203.6 to 74.5 µs. On a constant input the fallback path costs more than the original sort | Check equal scores, index order and the share of fallbacks |
| Merge residual, hyper-connections and RMS. Optimize the packing of K/V projections | On 2,048 rows the merge gave 924.8 → 609.0 µs with the same 3 outputs. For the Q8 K projection at T=3 and 16 heads another packing cut 10.09 to 5.72 µs | Keep the order of FMA and reduction. Check every shape that is really used, then the time of the full pass |

These are per-factor microbenchmarks. The final combination was checked separately on a long input.

## Stopped experiments (Hy4 Flash)

| Idea | The result that changed the decision | When to revisit |
|---|---|---|
| Recompute the DSA indexer through INT8 | In a T=2 microprofile the score stage was much shorter, but even an optimistic sum of preselection and exact recheck was 47.99 vs 46.93 µs for the FP16 path. In another test a merged histogram selection at T=2 gave 46.34 vs 58.31 µs in its own microbenchmark | Check other candidate shares, lengths and the full time of score → top-k → gather |
| Start independent operations earlier in decode | Early Engram computation gave about 1.4% in a paired control with 7,200 matching IDs. Several deep variants of SWA, early input feed and FFN overlap gave small effects that depended on width and depth | The profile points at the right part of the critical path. Compare a paired microtest, the first request and full generation |
| Copy the deep KV snapshot in advance, while prefill is still running | The final part of the copy went from 56.4 to 1.4 ms. But 3 adjacent blocks together took 2,256 → 2,257 ms, and the full HTTP request 91.206 → 91.276 s. A possible cause is PCIe contention in the neighbor block | Try again if a trace shows free bandwidth during the transfer |

## Prefill with every expert in VRAM (Inkling 2 Mini, 2026-11-21)

- The flash prefill kernel reads an FP8 cache: tiles fetched raw with `cp.async`, turned into FP16 in shared memory by a branch-free E4M3 decode.
- A looped model's message gates over a chunk as cuBLAS SGEMMs (from 32 tokens): +24% prefill, 2,500 tok/s at 4K-65K on one RTX 3090 (llama.cpp: 1,052).
- Prompts up to 96 tokens (a chat turn) read each expert once for up to 8 of its tokens instead of decoding it to FP16 for a GEMM: 12 new tokens 118 → 56 ms.
- Remaining cost at 16K, per 2,048-token chunk (721 ms): FP16 copies of NVFP4 experts 107 ms, expert GEMMs ~150, per-expert gather/SwiGLU/scatter launches 73, flash attention 66, dense GEMMs ~150. GEMMs straight over NVFP4 without the copies are the next step.

## Transfer checklist

1. Count bytes moved CPU→GPU per fresh token. If the same expert weights cross PCIe again and again, that is the first target.
2. Is DMA from pinned memory used for every tensor format in your quant? Check, do not assume.
3. Sweep chunk/block size on short and long inputs separately. Short prompts can lose.
4. Separate "tokens per loaded matrix" from "activations materialized at once".
5. With several cards: are the PCIe links busy at the same time? If 1 card pulls while the others idle, EP in prefill is on the table.
6. After every change that may fall back silently (EP, borrowed buffers, graphs), confirm in the log that the fast path is active.
7. Profile attention on long context. Expert delivery is not always the limit.
