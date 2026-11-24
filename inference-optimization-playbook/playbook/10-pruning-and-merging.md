# Pruning and merging experts (REAP, REAM, masking)

Covers GLM-5.3-Flash, Qwen3.6-35B-A3B, Laguna S 2.1 117B, MiniMax-M3.1-Flash, Qwen3.8-27B.

> **Read when:** someone proposes to drop, mask or merge experts. **Skip when:** the model fits in RAM + VRAM and the goal is speed. **Ledger tags:** `#pruning`.

The question that always comes up: if VRAM is short, why not drop the cold experts?

## How to score importance

- **Pick frequency is a bad importance metric.** For GLM the correlation of frequency with an honest REAP score (`mean(router weight × ‖expert output‖)` over the tokens where the expert was picked) was -0.22.
- **"Router weight × input norm"** is a good cheap substitute for REAP (correlation 0.947). You do not need to run the experts again.
- The REAP ranking is stable: correlation between halves of the data 0.994, overlap of the "keep 108" set 0.946.

## What the experiments gave

| Experiment | Result |
|---|---|
| GLM-5.3-Flash: keep 108 of 288 experts (-62.5%) by REAP | 19-22 of 25 tasks vs 4-9 for frequency and random selection. But we did not trust the model. The pruned build on 4 cards gave 10.8 tok/s. A month later the full model on another engine ran at 79 tok/s at 2 bits (fully in VRAM) and 43 tok/s at 3 bits (with offload to RAM). Engines, power limit and speculation differ (the full model had MTP length 1), so this is a reference point, not a clean comparison |
| GLM: the router is forbidden to pick experts outside VRAM (pruning on the fly) | KL to BF16 0.372 with 34% forbidden and 0.650 with 50%, vs 0.047 for the full 3.05 and 0.121 for 2.05. Forbidding a third of the experts is worse than losing a bit on all of them |
| GLM: REAM (merge removed experts into the remaining ones) vs REAP | layer output error 0.449 vs 0.146 for plain removal (1 layer, 991 positions, the metric is layer output, not model quality). Mean cosine between experts 0.016: they are almost orthogonal, there is nothing to merge |
| Qwen3.6-35B-A3B: half of the experts by a code calibration | HumanEval+ 146/164, same as the full Q4 and much better than the full Q2 (121/164). Prefill +25.7%, **decode +1.6%** |
| Laguna S 2.1 117B: REAP 50% | lost to the plain Q2 of the full model (HumanEval+ 119 vs 138) |
| MiniMax: skip RAM experts with a small routing weight | at threshold 0.45 top-1 98.9%, KL 0.0049. The quality gate was not passed |
| MiniMax: top-3 and top-2 instead of top-4 | NLL +46% and up to +250%, rejected |
| Qwen3.8-27B dense → MoE by slicing the FFN without training | GSM8K 83% → 5%. Neuron firing frequency is the wrong metric: 19.6% of "rare" neurons carried 20.9% of the energy |

## Conclusion

- Removing experts saves memory, not decode time, as long as the model fits (top-k is the same). It gives time only if it removes offload completely.
- Naive cuts (by "not in VRAM" or by frequency) break quality harder than losing a bit.
- REAP vs fewer bits: lost once (Laguna, a general task set), won once (Qwen3.6, code only). On wide MoEs and a narrow task you can remove half of the experts almost for free, but test on every domain that will be in the work ([03](03-expert-profile-and-placement.md): vision experts).
- **Our default recommendation: when VRAM is short, keep all experts in RAM and change the bits. Consider pruning only for a narrow task and with a check on every domain.** If RAM is enough, offload is almost always cheaper in quality.

## Transfer checklist

1. Does the model fit in RAM + VRAM at some quant? Then offload first, pruning last.
2. If you prune: score with REAP or "router weight × input norm", never with frequency.
3. Calibrate and test on every input type of the real load, including images, tables and other languages.
4. Compare against the full model at fewer bits with the same memory footprint. That is the real competitor.
5. Do not expect decode speed from pruning unless it removes offload.
