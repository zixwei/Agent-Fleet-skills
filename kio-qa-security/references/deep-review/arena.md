
# Arena

> pstack skill (MIT, see `PSTACK-NOTICE.md`), adapted for Claude Code (user-level, generic).

Keep a todo list with one entry per phase (Frame, Fan out, Cross-judge, Pick, Graft, Verify) before launching anything.

## Phase A: Frame

Every candidate gets the same prompt, so the prompt is the contract.

1. State the artifact each candidate is producing.
2. Derive the rubric, 3-6 concrete gradeable criteria for what success looks like on *this* task. It is the picker's tool in Phase D. Candidates only see the task.
3. Pick the runners. **Default: two runners, `model: "opus"` and `model: "sonnet"`, `subagent_type: "general-purpose"`.** Fable (`model: "fable"`) only after the user says yes for this run. There is no Gemini or other vendor. Repeat Sonnet for a third seat only when the work is generation-bound, not judgment-sensitive. Each extra seat costs Claude quota: say the seat count in your first line.
4. Give each candidate its own output location: `isolation: "worktree"` on the `Agent` call when it writes code, otherwise a scratch directory such as `$TMPDIR/arena-<slug>/candidate-<n>/`.

## Phase B: Fan out

Spawn all N subagents in one message (they run in the background by default), each with the task, the path to the shared grounding, its own output path, and instructions to produce the artifact and a short rationale naming the alternatives it considered and what it rejected.

If a candidate produces no output, proceed with N-1 and note the dropout.

## Phase C: Cross-judge

After all Phase B candidates complete, spawn one read-only judge: `subagent_type: "Explore"`, `model: "sonnet"` when you are Opus (and `model: "opus"` when you are Sonnet), with "do not edit files" in the brief. There is no other vendor, so say that the vendor check did not run. While you read in Phase D, the judge scores each candidate, by path label, on each rubric criterion and recommends a base.

## Phase D: Pick a base

Read every candidate end to end before picking. Score each against the rubric criterion by criterion, not on holistic feel, and compare with the cross-judge when Phase C ran one. Agreement on the base confirms the pick. Disagreement means one of you is biased or the rubric was ambiguous. Read both rationales before deciding.

Pick the base a future maintainer can extend most easily without breaking invariants. When two feel tied, prefer the cleaner boundary or smaller API.

## Phase E: Graft

Walk each losing candidate once more for what is worth porting into the base, usually one or two things per candidate, not most of it. Fold each graft in by hand, so the result stays coherent under one mental model. Don't paste mechanically.

If the candidates converge on one shape, that is strong agreement. Note it and ship the consensus shape. No graft is needed. If they wildly diverge, Phase A was under-specified. Reframe and re-run rather than averaging the divergence.

## Phase F: Verify

Verify the synthesized artifact against the real artifact, not a proxy like "it compiles". If verification finds a problem the arena missed, either Phase A was wrong (re-frame and re-run) or a candidate caught it and you missed the graft (go back to Phase E). Don't paper over it.

## Outputs

One synthesized artifact, with a short synthesis note alongside naming the base and why, the cross-judge's verdict or that Phase C skipped the judge, each graft with its source candidate, what was rejected and why, any convergence or dropouts, and the verification result.

## Project rules

Read the project's instruction files first (`CLAUDE.md`, `AGENTS.md`, and any rules they link) and put their resource and test rules in every subagent brief: how to run tests (and against which database), ports or services that must not be touched, limits on heavy jobs, file-size limits, branch base and PR rules. Never push, open a PR, comment on a forge, approve anything, touch production, or start paid runs from a candidate or adversary. Remove every worktree you created when you finish (`git worktree remove`).
