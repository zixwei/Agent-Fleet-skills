
# Interrogate

> pstack skill (MIT, see `PSTACK-NOTICE.md`), adapted for Claude Code (user-level, generic).

Adversaries try to break a change or a design. A finding survives only when a run proves it. Report the findings. Do not apply fixes.

## When

Use it for a diff of 300+ changed lines, or a diff touching an area the project's instruction file marks as risky (wire protocols, migrations, scheduling, concurrency, auth/permissions are the usual ones). For smaller diffs a normal review is enough.

## Frame

The scope is what the user names. Otherwise it is the branch diff from its merge base with the project's base branch (the instruction file names it; otherwise the default branch), through the working tree. State the intent with the **in-your-own-words** skill (read `(the in-your-own-words check, see kio-cognitive)`), from the user's message, the commits, and the PR description. Ask the user when the intent is unclear.

## Fan out

Run Phase A steps 3 and 4 and Phase B of the **arena** skill (read `arena.md`), with four changes. The reviewers are **one Opus and one Sonnet adversary** (`model: "opus"`, `model: "sonnet"`; Fable only after the user says yes). Each gets `isolation: "worktree"`; copy the scope's uncommitted changes into each worktree. Spawn each adversary as `subagent_type: "general-purpose"`. In place of the arena task and rationale, give each adversary the scope, the intent, the project rules (below), and this brief:

> Break this change. Prove each finding with a run against the real code, such as a failing test, a repro script, or command output that shows the wrong behavior. For a design or plan, run a prototype or probe the current tree. Keep repros in your own worktree. Report each finding with a title, the exact command, the output it printed, and the output the intent requires. Leave out anything you did not run. That covers style, naming, structure, and risks that need a future change to matter. No findings is a valid result.

## Verify

Verify each finding by rerunning its command yourself. Keep a finding when the output shows the failure and the failure breaks the intent. Drop the rest. Merge findings that share a root cause.

Skip arena Phases C, D, and E. Adversaries find different failures, so the verdict keeps every verified finding, not one adversary's list.

## Output

- **Intent**: the restatement.
- **Findings**: for each verified finding, the command, its output, how it breaks the intent, and the adversaries that found it. Say "no verified findings" when none survive.
- **Dropped**: for each adversary, the count of dropped findings and why.

## Project rules

Read the project's instruction files first (`CLAUDE.md`, `AGENTS.md`, and any rules they link) and put their resource and test rules in every subagent brief: how to run tests (and against which database), ports or services that must not be touched, limits on heavy jobs, file-size limits, branch base and PR rules. Never push, open a PR, comment on a forge, approve anything, touch production, or start paid runs from a candidate or adversary. Remove every worktree you created when you finish (`git worktree remove`).
