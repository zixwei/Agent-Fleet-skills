# The output head

Covers Hy4 Flash (vocabulary 120,832, BF16 head). Applies to any model where the projection onto the vocabulary is a visible share of the decode step, dense models included.

> **Read when:** the projection onto the vocabulary is a visible share of the decode step. **Skip when:** it is a few percent of the step. **Ledger tags:** `#head`.

## What worked: a cheap estimate with an exact recheck

The original projection reads BF16 weights for the full vocabulary. For greedy we tested a cheaper Q8 estimate plus a conservative error bound for every row. Rows that can still win are recomputed with the original weights. For the drafter the second candidate is checked too. On a tie the order of token IDs matters.

For sampling you need the original distribution or a separately verified selection algorithm that accounts for temperature, penalties and bias.

The proof for greedy depends on the number format, the reduction order and the rounding mode of the specific kernel. It does not carry over to another kernel for free.

## What had to be rolled back

Q4 only in the drafter head looked good: in the first A/B 176.37 → 182.60 tok/s with 4,400 tokens matching. All 510 IDs of the first turn in 9 additional dialogs matched too.

But a differently accepted last token shifted the snapshot boundary by 1 position. In the second turn of 1 Chinese dialog the 8th token diverged. The old config was restored.

A variant with an exact recheck in both heads matched on all 1,158 IDs and on the boundaries of 9 dialogs.

The lesson for any "safe" drafter change: a single-turn check is not enough. All first-turn IDs matched, but a last token that was accepted differently shifted the snapshot boundary by 1 position, so the second turn started from a different saved state. Run a 2-turn replay ([15](15-quality-and-release-gates.md)).

## What stayed in the lab

Q2 with a short candidate list and an exact BF16 recheck returned the original first 2 IDs on 720 of 720 recorded states. But a conservative bound by norms left a median of **120,832 of 120,832** rows for the recheck. So the guarantee cost at least a full pass.

On a new model, first look at the distribution of the number of candidates after applying the bound. The size of the initial list and success on a held-out set do not tell you the cost of a guaranteed answer.

## Sampling candidates on the device (Inkling 2 Mini, 2026-11-21)

Copying 200,058 logits to the host and sorting them cost ~0.8 ms a token. A histogram of 1,024 bins over the softcapped range finds the bin from which the top 1,000 start, and only those (1,000-2,400) go to the host: sampling 100 → 110 tok/s, the same tokens for the same seed.

## Transfer checklist

1. Profile the decode step. If the head is a few percent, skip this chapter.
2. Build the cheap estimate and a per-row error bound. Count how many rows survive the bound on recorded states. If almost all survive, stop.
3. For greedy: recheck survivors with the original weights, handle ties by token ID.
4. For sampling: keep the original distribution unless you have a verified selection algorithm.
5. Test over at least 2 turns with a snapshot in between, in several languages.
