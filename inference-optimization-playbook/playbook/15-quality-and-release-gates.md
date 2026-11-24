# What counts as preserved quality, and when to ship

Covers Hy4 Flash for the 3-level scheme and the release example. The rulers at the end come from all models.

> **Read when:** before any change ships, and when 2 builds give different answers. **Ledger tags:** `#quality-risk`.

## 3 requirements, 3 controls

| Requirement | Control | What we found on Hy4 |
|---|---|---|
| The implementation is identical on reference inputs | Teacher forcing: both builds get the same IDs. Compare **all** vocabulary logits at the chosen positions, including the edge of the context | The strict 4-card build reproduced 633 full rows of 120,832 FP32 logits. Against the previous verified release, 6,400 generated IDs and 15 review texts matched |
| The distribution is preserved within a chosen tolerance | On fixed continuations take the paired NLL difference of the actual token, KL over the vocabulary and the top-1 match. Build intervals over blocks of neighbor tokens | 1 card vs the previous single-card build, 4 sets, 2,560 positions: ΔNLL -0.0137, 95% interval [-0.0651; +0.0384], top-1 88.48%, median KL 0.00698. The interval allows both a small improvement and a small loss. It does not establish universal equivalence |
| The behavior of the application is preserved | Free generation, several languages and seeds, a tool call with a real result, vision, A → B → A, a changed suffix, the answer on the second turn | A single-turn check missed the error of the Q4 drafter head ([13](13-output-head.md)). In another build, with an unaligned cache hit and a repeat with temperature, the answer diverged at the 20th token |

Decide which of the 3 you need before the change. A kernel rewrite that should not alter arithmetic needs level 1. A new quant or a different chunk size cannot pass level 1 and needs level 2 with a stated tolerance. Everything that ships needs level 3.

## The router threshold on MoE

On an MoE, check the router margin itself. On Hy4 the gap between expert 16 and 17 could be about 0.0003. A change in summation order sometimes switched the expert even with the same original weights.

So a control permutation of the original experts is useful for reading KL ([09](09-quants.md)), and the strict branch still needs a separate bit-level comparison. An improved mean NLL on 1 synthetic text does not establish that the model kept its skills.

## Chunk size is a numeric change

Raising the chunk from 2,048 to 3,072 without a new numeric check: in an early strict series 1 of 33 top-1 positions changed and NLL rose from 3.961 to 4.147. In a later series on 512 positions no NLL difference showed up. One result detects a break of exact parity, the other estimates the mean effect. Decide in advance whether you need an exact repeat, and size the sample for the tolerance.

## Rulers from the other models

- A change that does not alter arithmetic has to be bit for bit at T=0.
- For everything else, build a ruler from the spread of stock itself. Ours: KL 0.0114 and top-1 0.969 between different chunk sizes of the same stock build. A change inside that spread is not a quality change.
- Add a battery of answers with a positive and a negative control, and read the output yourself. A quant that passed on logits produced garbage in Russian ([09](09-quants.md)).
- Freeze the live expert cache for any quality check. Moving 513 experts between CPU and GPU alone gives top-1 99.1% and KL 0.0028 ([04](04-live-expert-cache.md)).
- Other processes on the GPU can change the output through the placement: KL 0.023 from a smaller expert cache ([14](14-benchmarking-and-traps.md)).

## The release gate (Hy4 Flash)

The target was 2,500 tok/s prefill and 170 tok/s decode along the full length.

**Prefill passed.** On 4 cards build f44b304 processed **262,135 fresh tokens** over HTTP at an average prefill of **2,873.5 tok/s**, and the worst of 128 consecutive blocks gave **2,576.10**. This checks the blocks of a long input at a nominal size of 2,048. On a short request the fixed delay is large. On 1 card the full 261,888 input gave **1,422.5 tok/s**, 3.39× over its own base.

**Decode did not pass along the full length.** On 16 answers of 400 tokens f44b304 showed **187.67 tok/s** by total time and **159.35** on the worst answer. At depth 261,632 the worst rolling window of 32 tokens gave **132.49** on Chinese and **133.85** on English.

**The next candidate was rejected.** It improved the minimum on 15 source reviews from 164.47 to 166.94, but on fixed answers the mean fell to 186.00 and the minimum to 156.78. The texts matched. The previous build stayed.

These numbers answer 3 different questions: how long all answers took, how slow a full answer was, and what pause the user saw inside it. A release gate should name which of them it is about, and at what depth.

## When a "faster" candidate should not ship

- It is faster on average and slower on the worst block or the worst rolling window.
- It matches on the first turn and was not tested on the second.
- Its gain came from a short test and did not show on replay or free generation.
- It changed the numbers and the tolerance was not stated before the run.
- The fast path can turn off silently and nothing in the log confirms it ran.

## Transfer checklist

1. Before the change, write: which level of equivalence is required, the tolerance, the sample, the stop rule.
2. Record the noise floor: stock vs stock at another chunk, and a control permutation on MoE.
3. Run teacher-forced logits at short depth, deep context and the window edge.
4. Run the application-level battery: 2 turns, tool call, vision if present, A → B → A, several languages.
5. Report the mean, the worst block, the worst answer and the worst rolling window. Ship on the number the gate names.
6. Record the decision with the condition that would change it ([protocol/entry-template.md](../protocol/entry-template.md)).
