# The cost model: why MoE can be offloaded at all

Covers DeepSeek V4.1 Flash, Qwen3.8-Flash-Next, GLM-5.3-Flash, MiniMax-M3.1-Flash, July to November 2026.

> **Read when:** part of the experts are in RAM, or you are deciding whether offload will be fast enough. **Skip when:** the model is fully in VRAM (only the section on ceilings applies). **Ledger tags:** `#offload` `#decode`.

## The formula

An MoE model touches a small share of its weights on every token. Everything that does not fit in VRAM can stay in RAM, and you pay only for the experts that were picked on this token and are not on the GPU.

A miss is when the router picks an expert that is not in VRAM.

For decode with offload, one simple model of time worked on every engine we used:

```
time per token ≈ constant part + misses per token × cost of one miss
```

The constant part is dense weights (attention, shared expert, head), kernel launches and sync. The cost of a miss depends on where it is served: on the CPU from RAM, over PCIe into VRAM, or from disk.

Fits from our logs:

| Model, engine | Time model | What it shows |
|---|---|---|
| GLM-5.3-Flash EXL3 4.05, Maya | `t ≈ 9.5 ms + 866 MB / RAM-to-GPU bandwidth`. 45.9 ms on 2 channels (24 GB/s), 33.4 ms on 4 channels in pairs (35.8 GB/s) | GPU compute is only 9.5 ms. The rest serves 68.5 misses per token (46.7 computed on the CPU, 21.8 copied over PCIe) |
| DeepSeek Maya-S, Maya, one speculative round | `22.4 ms + 5.1 ms × rows + 13.4 ms × rows × miss rate` (R² 0.9997) | at 8-10% misses the CPU is ~10% of a round. At 50% misses it would be different |
| GLM 3.05, our exllamav3 fork (sim) | `16.1 ms + 0.75 ms × misses` | 19-23 misses per token with 66% of experts in VRAM |

## Routing skew decides everything

Offload pays exactly as much as routing is skewed. If a small share of experts gets a large share of picks, you keep that share in VRAM and misses are rare.

| Model | How picks are distributed | What it means |
|---|---|---|
| DeepSeek V4.1 Flash (live service profile) | top-50 of 384 = 51%, top-100 = 70%, top-150 = 81%, top-200 = 89%, top-250 = 95%, top-300 = 98%. About 194 "effective" experts per layer (108 to 268 across layers) | clear skew, static placement works |
| Qwen3.8-Flash-Next | on 1 GPU ~24% of experts in VRAM (UD-IQ4_XS) give 74-79% hits in speculation windows. On 2 GPUs ~53% give 92-95%. On IQ3_S with 69% in VRAM: 0.993-0.997 | strong skew |
| GLM-5.3-Flash | by third-party stats the "cold" half of experts gets 41% of picks. A fixed set built from our own trace cuts misses by only 10%. 0 dead experts on all 42 layers | globally almost uniform, static sets barely work. A live cache inside one request gives 94-98% |
| MiniMax-M3.1-Flash | random placement 61% hits, placement by profile 64-69%, oracle on its own answer 95-99% (sim) | the working set fits in VRAM, but every answer has its own |
| Hy4 Flash (16 of 512 per token) | on 1 long text, 220 experts picked by input stats covered 48% of picks during generation. Picked by the generation's own stats: 96% | locality exists inside an answer, but the prompt does not predict it |

How to check this fast on your model:

1. Record a router trace on 5-10 thousand tokens of your real load.
2. For every layer compute the effective number of experts, `exp(entropy of the pick distribution)`, and the curve "share of picks from top-N".
3. Simulate hits at your VRAM budget: static set by profile vs a random set of the same size.

If the gap in step 3 is under 10 points (MiniMax: 64-69% vs 61%), do not count on a static profile. You need a live cache ([04](04-live-expert-cache.md)), more VRAM, or both. The 10 point threshold is our heuristic, not a proven rule.

## Speed is counted in experts, not in gigabytes

The useful unit of VRAM is how many experts fit in it. That depends on bits per expert ([09](09-quants.md)) and on how much VRAM went to KV, prefill buffers and the drafter.

- Qwen Flash-Next on 2 GPUs: IQ3_S puts 16,869 experts in VRAM, UD-IQ4_XS 12,949, UD-Q4_K_XL 9,798. Prose decode 138-147 / 126-130 / 102-110 tok/s. A 128K context window instead of 262K gave +3-5% decode: the freed VRAM went to experts.
- GLM at 3.05 bpw (sim): share of experts in VRAM 66% → 36 tok/s, 72% → 41, 78% → 45, 85% → 51, 92% → 57. About +6% of experts in VRAM gives +4.5 tok/s.
- DeepSeek Maya-S (model from profile): +10% slots → -17% misses, +20% → -32%, +50% → -66%.

This elasticity drops once misses are cheap. On DeepSeek after all optimizations the CPU part was ~10% of a round, and +10 GB of VRAM for experts gives only about +1.8% decode (calc). At that point plain GPU work gave more: moving the Q8 matvec to int8 tensor cores gave +5.6% decode, bit for bit ([mw00/project-maya#810](https://github.com/mw00/project-maya/pull/810)).

**Before you free VRAM for experts, compute what share of token time goes to misses right now.**

## Know the ceiling before you start

The fit gives the ceiling for free: with zero misses a token costs only the constant part. Compute it before the work starts, report progress as a share of it, and tell the user early if the target is above it.

| Case | What the calculation said | What it meant |
|---|---|---|
| GLM-5.3-Flash EXL3 4.05, Maya | constant part 9.5 ms, so about 105 tok/s with no misses (calc). The real token took 45.9 ms on 2 channels (21.8 tok/s) and 33.4 ms on 4 channels in pairs (29.9 tok/s) | almost all of the time was misses, so placement, bits and the miss path were the right targets |
| GLM 4.05, target 55+ tok/s | needs no more than ~520 MB of RAM traffic per token, -40% misses (calc) | RAM bandwidth alone cannot get there, it needs more experts in VRAM |
| DeepSeek, +10 GB of VRAM for experts | about +1.8% decode (calc), with the CPU part at ~10% of a round | the constant part was the right target |
| DeepSeek, re-read of 1.5K tokens | floor 1.6-2.2 s (calc) against 2.9 s reached | little is left without PCIe 4.0 or a new overlap ([07](07-prefill.md)) |
| Qwen Flash-Next, any drafter | at most +20%, because at T=1 the model takes its own top token in 79% of positions | training a better drafter could not pay much ([08](08-speculative-decoding.md)) |
| Hy4 Flash, Q2 output head with a norm bound | the bound kept 120,832 of 120,832 rows for the recheck | a guaranteed answer cost at least a full pass, the idea stayed in the lab ([13](13-output-head.md)) |

For a model held fully in fast memory the ceiling comes from another calculation: bytes read per token over memory bandwidth for decode, FLOPs per token over peak compute for prefill. We have no data of our own on it.

## How to use this on a new model

1. Fit `a + b × misses` from 2-3 runs with different VRAM budgets for experts (or from a trace plus a miss counter).
2. Compare `a` with `b × misses`. If the miss term is small, stop working on placement and go to the constant part: kernels, attention, exchange between GPUs.
3. If the miss term dominates, decide between more experts in VRAM (fewer bits, smaller context, int8 KV), a cheaper miss ([02](02-serving-a-miss.md)), or fewer misses at the same budget ([03](03-expert-profile-and-placement.md), [04](04-live-expert-cache.md)).
4. Write the fit down next to the hardware config. A fit taken on 2 RAM channels says nothing about 8.
