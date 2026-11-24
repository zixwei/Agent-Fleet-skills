# Speculative decoding on MoE with offload

Covers Qwen3.8-Flash-Next (DFlash2, MTP), DeepSeek V4.1 Flash (DSpark), MiniMax-M3.1-Flash (DSpark), GLM-5.3-Flash (MTP, DFlash2), Hy4 Flash (MTP, DFly2).

> **Read when:** a drafter exists or you plan to add one. **Skip when:** requests are batched (speculation lost under batching here). **Ledger tags:** `#drafter`.

A drafter proposes a continuation, the target model checks it as a batch ("verify"). Useful speed is the sum of emitted tokens divided by the sum of drafter time, verify time and the rest of the step. The count of correct first proposals alone does not give this result.

## Why it pays

The time of a verify round is linear in the number of window rows, and the constant part is large:

| Model, engine | Cost of a round |
|---|---|
| Qwen Flash-Next, Strata, 1 card | 11.2 ms + 3.8 ms × rows |
| same, 2 cards | 9.3 ms + 3.1 ms × rows |
| DeepSeek Maya-S, Maya | 22.4 ms + 5.1 ms × rows + 13.4 ms × rows × miss rate |

Speed `= τ / (a + b × rows)`, where τ is tokens per round. Every accepted draft amortizes the constant part (dense weights are read once for the full window).

## Why the gain is smaller than on a dense model

Different rows of the window pick different experts, and every extra row wakes up its own cold ones. You can see it directly in the `rows × misses` term. On Qwen Flash-Next on 1 card, 40% of the window goes to experts outside VRAM, hits are 74-79%.

| Case | No draft | With draft | Speedup |
|---|---|---|---|
| Dense Qwen3.8-27B fully in VRAM, vLLM, DFlash2 | 75 tok/s | 206-273 | 2.75-3.62× |
| Qwen Flash-Next, Strata, 1 card, DFlash2 | 70 | 102-103 | ~1.46× |
| same, 2 cards | 78 | 112 | ~1.44× |
| DeepSeek, Maya, DSpark (Oct 24: old quant, 2 channels, before the main fixes) | 39-43 | 50-57 | 1.2-1.4× |
| MiniMax, maya-mm3, DSpark (synthetic prompts) | 17-25 | 21-29 | +16-24% |
| MiniMax, DSpark on 2K of real agent context, before the fixes | 27.0 | 25.0 | negative |
| GLM 3.05, our exllamav3 fork, with offload, MTP length 1 | 38.7 | 45.2 | +17% |

The current DeepSeek build was not rerun without the drafter. By the round fit above, the DSpark gain now may be around 1.8-2× (calc).

GLM (288 experts, top-8) and MiniMax (128, top-4) have weak global skew: a window of w tokens touches almost w × k different experts, with almost no overlap. A long window saves only dense weights and kernel launches. That is why on GLM in Maya DFlash2 is only 4% better than MTP (21.8 vs 21.0 tok/s), while short speculation works by itself (+17% for us; upstream on 4x3090 with offload reported DFlash2 at ×2 over no draft, ext). On MiniMax DSpark started to pay only after the cost-based window policy (below) and the CPU lane fix.

## Where acceptance is lost

- **Confidence threshold.** In Strata the default 0.35 put DFlash2 level with MTP, 0.5 gave +8-15%, 0.65 was worse than 0.5.
- **Draft vocabulary for the language.** The default DFlash2 draft vocabulary gave 32% acceptance on Russian. A Cyrillic one (59K tokens, a head 2.3x smaller) gave 54% and 78-81 → 94-101 tok/s, with English and code no worse. The cheapest large drafter fix in this history: no training.
- **Entropy of the model itself at T=1.** The model takes its own top token in only 79% of positions, so the ceiling of any drafter on Flash-Next is +20% (Russian +32%, reasoning +14.6%). Reasoning is 55% of the tokens of our answers.
- **Confidence cutoff.** DSpark on DeepSeek accepts 82-85% of what it proposes, but proposes only 2.1-2.4 drafts of 5. Without the cutoff, 3.21 tokens per round. A perfect drafter would give +50% (by the fit), a perfect cutoff +16%.
- **The quant of the main model did not affect acceptance:** DSpark on Maya-S and on UD-IQ3_S gave 3.21 and 3.19 tokens per round.

## What worked and what did not

| What | Result |
|---|---|
| DFlash2 instead of the built-in MTP (Qwen Flash-Next) | +7-13% over MTP, +44-46% over no draft |
| Cost-based window policy: length k with the maximum of E(k)/cost(k+1), the drafter is skipped when it does not pay (MiniMax) | +3-13% |
| MTP with draft length 1 (GLM, exllamav3) | 66-68 → 75.7. The default 3 gave zero or negative |
| Suffix lookup (draft from repeats in the context) | +24% on file edits, zero on normal text. 1.3% of rounds in a live session |
| Fine-tuning DSpine from DFlash2 weights (Qwen Flash-Next) | τ +4% against a threshold of +10%. 2/3 of the gain came from the vocabulary change, training is not needed |
| Fine-tuning DSpark on answers of our DeepSeek quant | zero: 3 variants (all weights, soft step, norms only) ended at 3.17-3.20 tokens per round vs 3.215 at the start. Error on held-out answers fell 3-4%, but first-candidate accuracy did not change, and only an exact match is accepted. Drafter errors come from small capacity (3 blocks vs 40 layers), not from a mismatch with the quant |
| Our own Domino drafter for AWQ | 4.8% slower than no speculation |
| Full DSpark drafter (MXFP4 experts) instead of the trimmed one | +1.3% by the model, but takes ~317 expert slots on GPU0 |
| Rejection sampling for DFlash2 instead of exact match | +3% (sim) |

Under batching speculation loses everywhere: n-gram in vLLM is +40% on 1 stream and -30 to -51% on 4-8 streams.

## Hy4 Flash: MTP vs a separate drafter

Compared: plain greedy, the native MTP at several widths, an adaptive width choice, and a separate 5-layer DFly2 that shares attention data with the target model.

| Attempt | What happened | What to check next on another model |
|---|---|---|
| Fixed K=1, 2, 3 and adaptive K | In the early build K=2 gave 126 / 114 tok/s on code / Russian, K=3 gave 127 / 99. The service got the adaptive choice | The verify cost of each width and the count of accepted tokens on code, prose and the languages of the target load |
| DFly2, K=5 | On code 3.7 tokens per step, but the step took 29.7 ms: 4.0 ms drafter, 25.7 ms verify of 6 positions. Result **126 vs 153 tok/s** for MTP K=2. On Russian the first token was accepted in 39% vs 61%, on English 66% for both | Worth returning to with a cheaper wide verify or another target model. Turning off hidden correction cut the result from 3.67 to 2.05 tokens/step: the mechanism worked, the loss was in the full cost |
| Copy the continuation from already seen history | In an offline replay of 2 deep answers EN/ZH, a suffix match of length 3 gave at most 0 / 3 accepted copied tokens of about 400. These answers have too few repeats | First count coverage on saved trajectories. Code with a repeating template can give a different result |
| Change verify for a group of proposals | BlockVerify on 144 requests gave practically the same speed, 152.96 → 153.09 tok/s. In an early MMVQ at T≥5 the numeric trajectory changed | Compare by identical IDs with plain greedy, at every supported width, with a rejected last proposal and at the window boundary |

### What drafter training gave (Hy4 Flash)

Tried: correction by a moving average of the hidden state error, a low-rank adapter, distillation of target logits, a predictor with activations of intermediate layers, and tuning the drafter temperature. These are separate tests of next-token prediction. The target model kept checking the proposals.

| Test | Result | Decision |
|---|---|---|
| Distillation and adapter | On 3,600 held-out positions the original MTP guessed 1,547, logit distillation 1,558. On 7 real generations the base gave 115.24 tok/s, EMA correction 112.96, suffix lookup 114.80, the chosen adapter 114.69. All 2,368 IDs matched | The gain on fixed inputs was too small to ship |
| Extra features from intermediate layers | On 94 held-out dialogs matches went 4,483 → 4,480 of 6,144. On 9 new generations 1,294 → 1,286 of 1,719. Temperature tuning over 3 confidence intervals gave only +0.018 points of expected acceptance on the held-out set | Kept the original drafter. On a new attempt, score acceptance on really generated trajectories |
| Switch between MTP and DFly, or verify an alternative branch | With a threshold picked on dev, the switch gave 2.423 tokens/step on a held-out replay vs 2.431 for MTP, adding about 0.15 ms. In simulation branching cut the number of verifies from 221 to 212 on 480 positions, but with the real cost of a wide verify the speed estimate dropped from 178.35 to 167.01 tok/s | The full tree was not built. A calculation on saved trajectories gave the decision before writing a multi-branch engine |

For reproduction you need independent dialogs in train/dev/test and a separate set of the model's own outputs.

3 models, 3 drafters (DSpine on Qwen, DSpark on DeepSeek, MTP on Hy4), the same outcome: training the drafter on your own data gave little or nothing. The vocabulary, the confidence threshold and the window policy gave more, with no training.

## How to evaluate a drafter without a week of integration

1. Take the window cost (`a + b × rows`) from the engine on an idle rig.
2. Take taps of the main model on your data and run them through a torch copy of the drafter, verified against the engine. On DeepSeek a copy that did not repeat the int8 rounding of matrix inputs diverged by 5% and gave different drafts in 6% of cases.
3. Simulate windows sequentially, the way the engine does: after a rejection the next window starts at the rejection position, with the same cutoff rule. For us this matched the live engine within 1-2%.
4. Do not score on fixed anchors (every 3 positions): this inflates τ (3.03 vs 2.50), because in the engine windows start more often in hard places.
5. Compute the ceiling in advance: a perfect drafter = always the argmax of the model. Before any training this tells you how much there is to win.
6. Write the success threshold down in advance.

## Count copied tokens separately (Inkling 2 Mini, 2026-11-21)

A suffix verifier on one RTX 3090 proposed continuations from earlier in the same request. The target checked every position. A repetitive CLI continuation reached 290.55 tok/s over 799 timed output tokens. Twelve verifier calls accounted for 744 input positions. On a sampled database explanation through the HTTP service the same build ran at 119.3 tok/s (one run each). Record the prompt, sampler, timed token count and number of verified positions alongside any result above the ordinary decoder's speed.

On an HTTP edit the user asked to return a Python file with one identifier changed. The verifier returned 509 tokens at 377.2 tok/s. The result matched the requested source byte for byte after removing the optional Markdown fence and trimming the outside whitespace. Fresh code, prose and sampled database explanations on the same profile ran at 124–127 tok/s. A grouped tensor-core verifier reached 434.19 tok/s on the repetitive CLI input. The official draft model instead averaged 2.56 committed tokens per four-position step on fresh Python. Drafting took 2.6 ms and verification 19.9 ms per step, for 113.86 tok/s overall.

The deployment with scalar expert products used a 96-token proposal limit. It returned the same 509-token Python edit at 488.9 tok/s. The target accepted 463 of 519 proposed tokens. Fresh prose ran at 113.4 tok/s over 367 tokens and fresh Python at 113.6 over 512. A separate experimental build retiled NVFP4 weights in memory and captured the official drafter's target verification in a CUDA graph. With 4,096 positions configured, fresh code reached 139.16 tok/s over 767 timed tokens. All 768 generated IDs matched the uncaptured verifier. Fresh prose reached 87.27 tok/s on the same build and width.

## A small window on a model with every expert in VRAM (Inkling 2 Mini, 2026-11-21)

The official DSpark drafter (3 dense layers, taps of the target's visits 5, 23, 41 and the final hidden) accepts, greedy, on one RTX 3090:

| Text | window 4 (3 drafts) | window 5 | window 8 |
|---|---:|---:|---:|
| prose | 2.18 | 1.91 | 1.90 |
| code | 2.66 | 2.86 | 3.04 |
| rewrite of a given text | 3.58 | 4.30 | 5.89 |

The verify window decides the gain, not the acceptance alone. With all experts in VRAM a plain step is 7.8 ms, and a window's experts are the union of its tokens' picks: 2.39× one token's at window 4, 2.76× at window 5, 3.59× at window 8 (traced over 255 tokens of prose and of code). The estimate for a well-built verify: ~12 ms at window 4, ~13 ms at window 5, plus ~2 ms of drafting, so prose barely breaks even and code gains ~+45%. A verify through the general prompt path cost 41 ms, which loses everywhere. Two external vLLM builds report the same shape: 2× RTX 3090 216 tok/s single user, one RTX 5090 prose +14% and code +67% over its own plain decode.

## Transfer checklist

1. Fit the verify cost per width on your engine and placement. Include the miss term if experts are offloaded.
2. Count accepted tokens per millisecond of the *full* step on code, prose and every language of the load.
3. Check the confidence threshold and the draft vocabulary for your languages before anything expensive.
4. On models with weak skew, prefer short windows or a cost-based window policy.
5. Verify changes are numeric changes. Compare IDs against plain greedy at every width, including a rejected last proposal and the window boundary ([15](15-quality-and-release-gates.md)).
6. If you serve batches, test speculation under the real concurrency. It can flip to negative.
