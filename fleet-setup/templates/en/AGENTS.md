After a restart, session change or context compaction, read the project card and the latest handoff addressed to your role or task, check them against the actual state, then act; before wrapping up or running out of context, write a handoff first.

# AI Fleet project card

## Goal

〔待定：The project's long-term goal, what the fleet will deliver, how its work will be checked, and which decisions stay with the human〕

Only the project manager role maintains this card. Other roles propose changes through handoffs and the decision log.

## Rules

〔待定：Rules in effect, one per line. Record proposed trials in the opportunity log and matters for the human under Decisions needed〕

Treat a compacted summary as a clue, not as evidence; verify the project card and matching handoff before acting. Record important human decisions, new rules and newly started background tasks immediately in the decision log or handoff; reconcile them at closeout.

When the human says “Continue where we left off,” check first, then open the reply with three lines: where we stopped; what was done since the last handoff; what is waiting for the human. Then go on.

Do not let work stop at a milestone: queue the next several items in the handoff, past the milestone; when one item waits for the human, have the project manager record it under Decisions needed and do other work; if nothing can be done, ask the project manager to assign work (the project manager tells the human and proposes one or two items to queue next) instead of waiting.

When human help is needed, explain what to do and why, give only one step at a time with where to do it and an exact command when available, and say what to reply here; check the reply, using a read-only check where possible, before continuing, and if it fails give the next step without restarting. Never ask for secret keys or passwords or have them pasted into the conversation; obtain the human's agreement before spending, external commitments, deletion or direction-setting.

## Work in progress

〔待定：Role, task, delivery location, and current state〕

## Decisions needed

〔待定：Date (YYYY-MM-DD), role waiting, specific action, recommendation, reasoning, and decision maker〕

## File map

The working directory is the project root containing this file.

Shared working files live in `fleet/`:
`fleet/principles.md` · `fleet/values.md` · `fleet/members.md` (roster) · `fleet/decisions.md` · `fleet/opportunities.md` · `fleet/handoff.md` · `fleet/approvals.md` (approval table).
Role descriptions live in `fleet/role-cards/`, one brief per task in `fleet/task-briefs/`, and results in `fleet/deliveries/`.
Root `CLAUDE.md` imports this file with `@AGENTS.md`. Members invoked by a command perform only the work authorized in their brief.
