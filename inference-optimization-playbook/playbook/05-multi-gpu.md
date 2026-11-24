# Several GPUs: layer split, TP, EP and the exchange between cards

Covers Qwen3.8-Flash-Next, GLM-5.3-Flash, DeepSeek V4/V4.1 Flash, MiniMax-M3.1-Flash, Hy4 Flash, all on 4x RTX 3090 with PCIe 3.0, P2P on and without NVLink.

> **Read when:** there are 2 or more cards. **Skip when:** there is 1 card. **Ledger tags:** `#multi-gpu` `#layer-split` `#tp` `#ep`.

## Layer split (pipeline by layers)

Strata and all our Maya forks for DeepSeek, GLM and MiniMax work this way. Layers are cut into contiguous pieces, the last card holds the head and the drafter. There is 1 hop between cards per token and no all-reduce. The main property for offload: **every card holds its own experts and pulls misses over its own PCIe link**.

The downside: in decode the cards work strictly in turn. On MiniMax, by nsys, 2 or more cards were busy at the same time 0% of the time. The extra VRAM goes almost entirely to experts.

Qwen Flash-Next in Strata, decode by number of cards (UD-IQ4_XS, 16K essay, RAM 4 channels NPS2):

| Cards | 1 | 2 | 3 | 4 |
|---|---|---|---|---|
| Decode, tok/s | 90-93 | 121-130 | 127-129 | 118-129 |
| Prefill 64K, tok/s | 1,930 | 3,390 | 3,850 | 4,080 |

The second card gives +31-38%: on 2 cards hits are 92-95% vs 74-79% on 1, and the CPU computes much less (2-3 ms of a window vs 9-12). On 8 RAM channels 1 card got 8-18% faster and 2 cards did not change, so the gain from the second card there is smaller, about +15-24%. The third and fourth cards add nothing to decode: every stage is one more transfer and misses are already rare. Only prefill on long prompts grows.

A mode where the layers are on 1 card and the other cards are "helpers" for experts was 25-30% worse than layer split (77 vs 105 tok/s on 2 cards, on IQ3_S and the old Strata 0.1.40).

Idle cards in the pipeline can be used:

- by speculation: DFlash2 on 3+ stages runs as a pipeline, neighbor tokens of the window on different cards ([08](08-speculative-decoding.md));
- by MTP with 2 parts of the pipeline: the first half of the cards already computes the next position;
- by expert parallel in prefill ([07](07-prefill.md)).

## Tensor parallel (TP)

| Case | Result |
|---|---|
| GLM, everything in VRAM (2.05 bpw) | layer split 48.3 → TP4 66-68 tok/s. TP wins when there is no offload |
| GLM, prefill in TP | limited by all-reduce through the host (26-29 ms per tensor, ~70% of the pass). Our own card-to-card exchange over P2P in 2 rounds: prefill 961 → 1,447 tok/s |
| GLM, decode in TP with the same P2P exchange | -6% (63.4 vs 67.7): the stock transport takes 8 µs per call, and everyone waits for the slowest rank |
| DeepSeek, TP=2 with P2P | decode +7%, but reading a 344K prompt is 2.2x slower, EP does not work under TP, 6,170 experts in VRAM instead of 7,352. Dropped |
| MiniMax, TP only for attention | decode +3-10%, prefill -4.8% (the P2P gather of weights used the same PCIe as the expert stream). Left behind a flag |
| Hy4 Flash, 4 cards, layer split instead of TP | decode with MTP was 25-35% slower than TP. 2 microbatches in prefill also slowed the pass, and a multithreaded variant diverged from the reference |

Why TP combines badly with offload: a miss on any rank is on the path of every layer, and everyone waits for the slowest one. Stock exllamav3 forbids expert offload in TP completely, we had to write our own RAM tier for TP.

The rule from the first 4 models: **if the model is fully in VRAM, TP. If experts are offloaded, layer split.**

Hy4 Flash gave one counterexample. In upstream Maya v1.1.69, with a CPU lane serving the missing experts, TP under partial placement beat the layer-by-layer fallback (see "TP with partial placement" below). That is 1 model on 1 engine version, so test it on yours.

## Expert parallel (EP)

| Where | Result | Why |
|---|---|---|
| llama.cpp, DeepSeek V4 fully in VRAM | prefill +20.9%, decode -34% | +345 µs per MoE layer: host sync, the launch ladder of 4 subgraphs, imbalance. The all-reduce itself is 28-36 µs |
| Maya, DeepSeek with offload, prefill only (our patch) | re-read of 1.5K -42%, 4K -9 to -13%, turns -13.6% | while the owner card computes its layers, the 3 other cards pull part of its cold experts from RAM over their own links and compute them |

Details on EP in prefill are in [07](07-prefill.md).

## The exchange between GPUs in TP (Hy4 Flash)

**Symptom.** On 4 cards about 30% of decode kernel time went to all-reduce. For 1 token the messages are tiny. The tail waited for flags to appear on the neighbors. In batch prefill the message sizes and the options for overlap are different.

| Change | Result on Hy4 | What to transfer |
|---|---|---|
| In decode, send 2 BF16 values and the step number as one 8-byte word. The receiver sees ready data by the step number | Verify of 1 position: 12.64 → 11.22 ms. MTP K=2 on code: 145.3 → 153.0-153.6 tok/s, on Russian: 129.0 → 135.5-136.1. Decomposing the tail of the reduction saved about another 2.5% of T=1 time in a separate test | Time latency at the real message size and check the rounding order on all participants. For another topology compare transports again |
| In prefill, pack data locally and send through peer DMA with the same summation order | On the 2K-8K segment the first variant took 1,958.6 to 2,363.5 tok/s. After overlapping the exchange with compute, the candidate gave 2,801.84 tok/s on the full window, minimum block 2,723.13 on a warmed engine | Time separately: copy gaps, independent work, wait time, and a logit comparison. The last number is for the full candidate |
| Larger blocks and more threads in the push, fewer fences, sending to all neighbors at once | More parallelism did not help. One shared fence improved T=1 but hurt T≥2. Just shrinking the format from F32 to BF16 with the old flags did not remove the wait | Test the real verify widths. One short transfer between 2 cards is a poor model of simultaneous writes between 4 |

The first packed query variant computed RoPE differently. Going back to the rotation function of the reference build restored bit parity at the checked positions. When you pack anything for exchange, compare against the reference before you look at speed.

## Each expert on 1 card (Hy4 Flash, upstream Maya v1.1.69)

One quantized expert had 6 blocks. On 4 GPUs, equal-size slots needed room for 2 blocks on every card, 8 instead of 6. The memory plan assumed ideal division by 4, so loading could end in OOM.

The fix assigns each expert to 1 card in full and counts the real size of the layout. On IQ2 with the same chunk 1,024 and MTP K=2, VRAM use of the group dropped from 79.3 to 65.5 GB and decode grew from 121.8 to 128.1 tok/s. The original layout with another chunk matched itself in top-1 at 89.5% of positions, the new one matched the original at 88.1% on 513 positions.

On another model, first compute the real padding from the tensor shape, the quant block size and the number of devices. [mw00/project-maya#1062](https://github.com/mw00/project-maya/pull/1062)

## TP with partial placement (Hy4 Flash, upstream Maya v1.1.69)

In the original planner, not having VRAM for *all* experts switched execution to layer-by-layer mode. After the change the cards kept computing in parallel and the CPU lane served the missing experts.

On Q3_K at similar residency (71% and 70%), prefill of 57,587 tokens grew from 695-696 to 1,098-1,101 tok/s, decode of short code from 54.7-55.9 to 67.1-68.9.

Two things came up:

- Hyper-connection buffers had to be allocated in advance: `cudaFree` under a shared lock caused a 60-second stall during warmup.
- There were periods when the CPU lane with drafts slowed down about 2x on both builds. The cause was not found. The published series were taken outside those periods.

These numbers describe the port into v1.1.69. [mw00/project-maya#1068](https://github.com/mw00/project-maya/pull/1068)

## Transfer checklist

1. Is the model fully in VRAM at the quant you want? TP. Compare with layer split on your real load anyway.
2. Offloaded: start with layer split. Count how many cards actually improve decode (on Qwen it was 2).
3. TP with offload worked for us in 2 forms: a VRAM cache with high hits (GLM, our exllamav3 fork) and a CPU lane serving the missing experts (Hy4 Flash). Time the slowest rank.
4. Profile the all-reduce separately for decode (tiny messages, latency) and prefill (large messages, overlap). The best transport differs.
5. Count the real padding of expert slots per card before trusting a memory plan.
6. Compare on the same number of requests, the same depth and the same weight placement. A batched server load needs its own test.
