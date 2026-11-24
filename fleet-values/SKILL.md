---
name: fleet-values
description: Use when the fleet needs to agree on or revise a value. Gather evidence and dissent, identify who can decide, and link each choice to its reasoning.
---

# Define the fleet's values together

Start with a real choice the members faced. Ask which principle should guide the next such decision and what evidence supports it. Record the decision and substantive disagreements so the next member can recover the reasons.

Read and write working files only inside the project chosen by the reader. Start with the root project card, `fleet/members.md`, the approval table, and existing principles, values, and decisions. Preserve confirmed contents and find the original reasoning before revising them. If the roster, project manager, or authority for this kind of decision is missing, explain the first gap and why it matters. Ask for one participating role, the role serving as project manager, or the missing assignment in the approval table, as applicable. Check the answer against existing records before asking for anything else. The paths below are for English projects. For Chinese projects, use the entrypoint installed with `--lang zh` and follow the project card to preserve the existing Chinese filenames.

Follow this sequence:

1. Ask each member for a few proposals. Each proposal states the value, the AI behavior problem it addresses, the evidence, and concrete actions to take and avoid. Distinguish observations, inferences, and guesses; say when no direct observation exists. Collect proposals through the project's existing invocation methods and preserve their sources. Do not speak for members who have not participated; list missing submissions.
2. Have the project manager summarize. Merge duplicates, preserve substantive disagreements, and explain choices among candidates. Keep the list short and each value substantial, with observable behavior. Do not choose values on the reader's behalf or make an existing set the default.
3. Give each candidate, its evidence, unresolved disagreements, and the project manager’s recommendation to peers for review, then to a model from another provider for a second check. Record both reviews. If a review is missing, keep the item pending. The human decides matters of direction and can weigh in at any time. Within the agreed direction, the role authorized in the approval table decides changes to rules for how members act. Ask the decision maker to confirm the exact wording of one item or specify a revision. Keep the item pending until there is an explicit decision.
4. After confirmation, write the agreed values in `fleet/values.md`, starting from the [English empty template](../fleet-setup/templates/en/values.md). Each value states the behavior problem addressed, actions to take and avoid, scope, and evidence of confirmation. Unconfirmed items remain candidates and do not take effect.
5. Append the decision to `fleet/decisions.md`. Record reasons for adoption or rejection, dissent, and how it was handled; link the decision and value entries to each other. If dissent has no answer, mark it unresolved instead of rewriting it as agreement. Reference earlier decisions when revising and preserve the history.

Reopen the files. Check that each confirmed value has actions to take and avoid, both reviews, and the decision maker’s confirmation. Distinguish candidates from active values and verify references inside the project. Return agreed values, pending items, missing proposals, and dissent locations. For a pending item, name the next reviewer or decision maker and the point to resolve. If collecting a proposal needs help with a member’s invocation method, use fleet-dispatch to guide that step, verify the reply, and resume collecting. Never request keys or passwords. External commitments, spending, and deletion also require human approval first.
