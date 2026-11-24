# The live expert cache

Covers DeepSeek V4.1 Flash, MiniMax-M3.1-Flash, GLM-5.3-Flash, Qwen3.8-Flash-Next, Hy4 Flash.

> **Read when:** experts move between RAM and VRAM while the engine runs, or a static set is close to random. **Skip when:** everything fits in VRAM. **Ledger tags:** `#offload` `#weak-skew` `#strong-skew`.

A live cache moves an expert from RAM to VRAM when it gets hot in the current request and evicts one that cooled down.

## Implementations we ran

| Engine | Policy | Parameters |
|---|---|---|
| Maya for DeepSeek | heat with decay. An expert moves up if its heat > heat of the coldest one in VRAM × 1.25 + 1 | decay ×0.9 every 256 tokens, up to 8 swaps at once, 1 per layer |
| maya-mm3 for MiniMax | decaying counters (half-life ~230 tokens), async swap on a separate copy stream | up to 8 moves per token per model, no more than 4 in flight per layer |
| Our exllamav3 fork for GLM | CLOCK cache in VRAM on top of a fixed hot set | 180-480 slots. Admission to the cache only for small batches |
| Strata for Qwen | `--expert-cache auto`: sized to free VRAM, adapts to routing | the first requests after start are slower |
| Maya for Hy4 Flash | start from the profile of past answers, change the GPU cache content by decaying frequency | after prefill the freed VRAM also goes to experts (see below) |

## What different policies gave (MiniMax, simulator on traces of real answers)

| Policy | Hits |
|---|---|
| static placement by profile | 64-69% |
| random placement | 61% |
| 1 swap per token | 72-83% |
| up to 8 swaps per token (service) | 87-93% |
| LFU with decay, budget 8 | 89-96% |
| LRU that adds on every miss | 93-97% (but 6-15 moves per token) |
| oracle: best static set per answer | 90-97% |
| Belady (perfect eviction) | 97-99% |

Capacity vs misses on MiniMax (simulator with the service policy): 56 slots per layer → 82-91% hits, 64 → 87-93%, 72 → -25% misses, 80 → -44%, 96 → -70%. One slot on all 57 layers costs 1.03 GB. Another analysis with the LRU policy showed the opposite: +7 slots per layer add only 0.7-1.6 points. The better the cache policy, the less capacity matters. In the live MiniMax service hits were 71-94% by logs and 85% on a replay of real sessions.

On GLM a live cache of 480 slots on top of 50% fixed experts gave 94-95% hits. The simulator showed that with 66% of experts in VRAM you get 19-23 misses per token under any split of memory between the fixed set and the cache.

## Hy4 Flash: adaptive cache on 1 and 2 GPUs

| What we tried | Result | How to check transfer |
|---|---|---|
| Start from the profile of past answers and change the GPU cache by decaying frequency | After 57K on 2 GPUs a 600-token generation gave 81 tok/s, a 1,200-token one 91-97 as hits grew. In a real session the profile changed together with the task | Alternate topics. Compare a start with an empty cache, a warmed answer and a return to the earlier topic. Record the time spent moving weights |
| Give the free prefill memory to experts during decode | On 1 GPU the permanent and the temporary sets were merged into one adaptive cache. The CPU also parallelized the quantization of intermediate activations. These are parts of the single-GPU build from [07](07-prefill.md) | Compare the number of resident experts, hits and latency on the transitions prefill → decode → new prefill. Fix the budget for both runs |

A long control with the same task in an agent harness (DSH) gave for the final 2-GPU base: 47 calls, context up to 97,834, average decode 74.2 tok/s, range 51-87, VRAM hits 79.8%. An early version of the CPU lane without prefetch got 47.8 tok/s in another session on the same task. The agent trajectories differed, so the gap between the sessions cannot be assigned to one fix.

## Observations

- **Short answers do not warm the cache.** Agent tool calls (23-132 tokens) on MiniMax got 71-76% hits vs 85-93% on long answers.
- **A live cache makes output non-deterministic.** An expert runs on the GPU one time and on the CPU the next, so summation order and rounding differ. MiniMax service answers matched at temp 0 in only 1 case of 99. Moving 513 experts between CPU and GPU gives top-1 99.1% and KL 0.0028. This is normal, but freeze the cache when you test quality.
- **Warming from the prompt after prefill.** Idea: after reading a long prompt, immediately move up the experts that were hot in it. On DeepSeek (`DS4_SETTLE_MS`) a short test showed +21-24% decode, and free generation showed zero with TTFT +0.3-1.9 s. Rejected. On MiniMax the same trick (copying the expert from the prefill buffer, ~20 µs instead of 1.25 ms over PCIe) stayed in the plan.
- **A separate profile refresh for the task** (refresh on 24 prompts) did not help on DeepSeek: the live cache catches up in 1-2 requests. Upstream on AMD hardware reported the same trick lifting hits on the first answer from 42 to 75% (ext). It did not reproduce for us.
- **A "fast" live cache** (less hysteresis) on DeepSeek was worse: 36.4 vs 39-40 tok/s. Frequent swaps cost time by themselves.

## Predicting the next experts

| What | Result |
|---|---|
| Loading the "almost picked" experts of the previous token (GLM) | did not work: 22.9-30.9 misses vs 23.2. Neighbor tokens pick different experts |
| Predicting the router of layer L+1 from the input of layer L (MiniMax, offline) | top-8 catches 84-90% of the real picks, top-16 93-95%. For L+2 top-8 catches 73-83% |
| Same for GLM (sim) | 1 block ahead, 75% of the real 8 are in the top-8. Estimate +20-28% decode at 3.05 bpw. Not implemented |

The limit: an expert takes 1.4 ms over PCIe 3.0 and a layer takes 0.4-0.7 ms. Prefetch only makes it 2 or more layers ahead, where prediction is worse. On our hardware this is more of a hint for the cache and for the CPU work order than real prefetch. With PCIe 4.0/5.0 it looks better.

## Transfer checklist

1. Run a cache simulator on real router traces before writing engine code: static, random, your policy, Belady. The gap between your policy and Belady is your headroom.
2. Sweep capacity with your policy. If +10% slots barely moves hits, capacity is not the lever.
3. Count swaps per token and their cost. A policy with more hits and many more moves can be slower.
4. Test on short answers separately (tool calls). They see a colder cache.
5. For any quality check, freeze the cache content.
