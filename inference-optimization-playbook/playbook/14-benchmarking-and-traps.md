# How to benchmark, and the traps

Covers all models. This chapter is about getting numbers you can trust. What counts as "quality preserved" is in [15](15-quality-and-release-gates.md).

> **Read when:** always, before the first number. **Ledger tags:** every row.

## Build a reference before you optimize (Hy4 Flash)

**What we did.** The first working llama.cpp build was the source of input IDs and intermediate values. For the new port we built a host F32 forward and compared the stages of a layer before moving compute to the GPU. Then we compared separately: a normal step, batch prefill, and the verify of several MTP proposals. If an error appears at the 5th position of a batch, single-token decode will not find it.

**What the original engine showed.** BF16 experts of the MTP block took about 7 GiB. Requantizing only this block, first to Q8 and then to Q4, freed room. On the llama.cpp patch Q8 KV gave corrupted text already at 32K. The full config ran with F16 KV, part of the experts and the vision projector on the CPU. Its prefill at 4K / 32K / 128K was 469 / 472 / 435 tok/s. Even unrelated CPU load affected the chat. Later Maya could work with Q8 KV, verified in its own implementation.

**How to transfer.** Save the tokenized input, the chat template, the projector, the sampling mode, the verify width and several full rows of logits. Fix the resources of other processes. The same quant name in 2 engines does not verify the KV format, the scaling or the read path. A reproducible intermediate dump lets you find the first divergence before errors pile up in text.

## How to run the numbers

- **Replay real sessions.** Our corpus: 4 real agent sessions (2 Claude Code, 2 Codex), 99 requests, windows 69-298K. The answers are forced on the engine (a replay hook), so every variant walks the same text. A vs A spread is 0.06% on the session sum.
- **Time decode only on free generation** or on a fixed length at different depths. Forced answers inflate draft acceptance (0.89-0.90 vs 0.83-0.86) and decode.
- **Write thresholds down in advance.** Ours: 0.5% on the session sum, 1.5% on the median read, 5% on decode. Anything smaller is noise.
- **Compare only inside 1 ABAB run.**
- **Triad before and after every run.** If bandwidth dropped after the run (heat), the run does not count.
- **Throughput.** For sequential requests use the sum of tokens divided by the sum of time. For concurrent load count total output over total wall time.
- **3 questions, 3 numbers.** How long did all answers take (sum), how slow was the slowest full answer (minimum), and what pause did the user see inside an answer (worst rolling window). A candidate can improve one and lose another ([15](15-quality-and-release-gates.md)).
- **KL only on one panel** (one reference, the same windows). For GLM we kept a panel of 25 windows × 2,048 tokens with BF16 logits.

## Traps we stepped on

| Trap | What happened |
|---|---|
| Short test | promised UD +27% decode, real generation +2%. Cache warming after the read: +21% vs +0.3% |
| Synthetic vs real content | MiniMax 42 tok/s on "write an analysis" vs 26 on real sessions |
| The miss counter includes prefill | the log line "CPU lane N% of the experts" showed 0.1-0.9% at depth, and on decode it was 8-15% |
| A test without the cache vs a service with the cache | the CPU lane share was inflated 2x (29-36% of picks from RAM vs 71-94% hits in the service) |
| The profile keeps growing | A/B arms changed each other's placement |
| Silent fallback | EP with no VRAM. DFlash2 in Strata when the model name does not match the pattern (MTP was used instead). Snapshots after a block size change on Hy4 |
| RAM config | NPS1 halves, 7 sticks, overheating. Numbers from different configs got mixed |
| Power limit | points at 220/250/270/300 W were next to each other in the results database. A "+91% from watts" turned out to be a harness change |
| What watts give | equals the share of time that really computes on the SM: TP=1 220 → 350 W gave +78%, TP=4 220 → 250 W gave zero (different windows) |
| Other processes on the GPU | ComfyUI cut the expert cache (9,830 vs 9,976), and that alone changed the output (KL 0.023) |
| Abandoned `nvtop` | it polled the driver, and generation dropped to 11-20 tok/s |
| An orphan on the port | an interrupted benchmark left a server, and the next series benchmarked that one |
| Idle calibration | Maya's planner promised 114 ms per token, the real number was 42-49. A GLM calibration on EXL3 set 11 CPU threads for 4 cards, and DFlash2 fell 21.8 → 8.1 |
| Someone else's numbers | runs on 8 cards with 99% of experts in VRAM were taken as an estimate for 4 cards with 56%: expected 55-70, an honest recount gives 36-42 (calc) |
| Time by SSE chunks | 1 chunk carries several tokens. Take the engine timings |
| Automatic budget | the same binary could place a different number of experts between runs, which affected the comparison of NVMe modes ([03](03-expert-profile-and-placement.md)) |
| Unified code paths | 1 extra field in a struct passed by value cost 2-3% of prefill. A loop over 4 expert slots added about 0.35 ms to short decode |

## Traps on a looped model with an FP8 cache (Inkling 2 Mini, 2026-11-21)

- Any 2 runs over an FP8 (E4M3) cache drift apart within a few tokens: a difference of 1e-5 flips codes of a 3-bit mantissa. Compare paths by full logits on forced tokens, not by the generated text.
- The mean KL over forced tokens is set by a few outliers. Use the median and the count of changed top tokens, and calibrate them against a change you already accepted on the same text.
- A CLI run of the engine without a context size did not keep every expert in VRAM and took the general path: checks of the resident path without it checked nothing.
- `nsys` does not see kernels inside a CUDA graph (profile with the graph off), and `nsys stats` does not overwrite an older CSV.

## What to record with every number

- model, quant, engine and revision, full launch arguments;
- hardware: cards, power limit, PCIe generation, RAM channels and NUMA mode, Triad before and after;
- context depth, share of fresh tokens, KV precision, chunk/block size;
- placement: experts in VRAM per layer, scratch sizes, free VRAM after start;
- cache states: prompt cache, expert cache (frozen or live), OS file cache, profile copy;
- load: content type, language, sampling mode, seed, number of concurrent requests;
- what else was running on the box.

Numbers taken on different RAM configs, power limits or harnesses do not compare with each other.

## Transfer checklist

1. Build the reference and the parity checks first. Speed work on a wrong port is wasted.
2. Collect a replay corpus of your real sessions before the first optimization.
3. Decide the thresholds and the stop rule before the run.
4. Keep the short test for debugging only. Decide on replay and free generation.
5. After every run, grep the log for fallbacks and confirm every fast path was active.
