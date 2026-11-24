---
name: fleet-setup
description: Use when someone wants to build a fleet for an ongoing project. Use their project materials to define roles, deliverables, reporting lines, reviewers and decisions for the human. Draw the org chart, then draft the first task brief. Also use to draft or submit an AI Fleet Handbook issue.
---

# Build a fleet for a project

Read the existing project material first. Identify the long-term goal, who does what now, and which tools and rules the project already uses. Choose roles for the work the project actually needs. Propose a standing role once that kind of work recurs. For handbook feedback, go straight to [Report an issue](#report-an-issue-to-the-ai-fleet-handbook).

The project manager divides the work, writes briefs and arranges acceptance. Members deliver the work and the designated reviewer checks it against the agreed criteria. If they disagree, take the open question and the evidence on each side to the role authorized to decide under the approval table.

If roles are already assigned, restate the assignments in the project’s own words, check them with the reader and fill only the gaps. If the project has no assignments yet, use its materials to propose the roles it needs and who should check whose work. Ask one question at a time when the answer would change the assignments. For example, if two documents give different acceptance criteria, ask which agreement is in effect. Explain why the answer matters and ask the reader to reply here. Check their answer and follow up only on the missing detail. Reuse answers already given. When the materials are sufficient, start by returning the roster in this reply.

Work from the project root chosen by the reader. Inspect existing root `AGENTS.md`, `CLAUDE.md`, and files in `fleet/`; preserve their contents and merge changes individually. Do not replace agreed principles or assignments on the reader's behalf.

Use the [English project card](templates/en/AGENTS.md) and the templates in `templates/en/` for English projects. Copy them directly to the English project paths below, with no copies elsewhere, and reconcile any legacy paths in the template's file map against this list. For Chinese projects, install with `--lang zh` and use that entrypoint and its templates to preserve the existing Chinese filenames:

```text
project-root/
  AGENTS.md
  CLAUDE.md
  fleet/
    principles.md
    values.md
    members.md
    decisions.md
    opportunities.md
    handoff.md
    approvals.md
    role-cards/
    task-briefs/
    deliveries/
```

Keep the project card's contents in root `AGENTS.md` only. A new root `CLAUDE.md` contains only `@AGENTS.md` on its own line, as in the [import template](templates/en/CLAUDE.md). If either file already exists, preserve it and append an “AI Fleet project card” section: put the card in the `AGENTS.md` section and only the import line in the `CLAUDE.md` section. For Chinese projects, use “AI 舰队项目卡” as the section title. If the section already exists, merge within it instead of adding another; leave other sections alone. Explicitly read the root project card at startup and check whether the host has loaded the import.

Copy the selected template's first line verbatim. It states both actions: after a restart, session change or context compaction, read the project card and the latest handoff addressed to your role or task, check them against the actual state, then act; before wrapping up or running out of context, write a handoff first. When appending a section to an existing file, put this line first in the new section's body.

Copy the remaining templates of the same language into `fleet/`: [principles](templates/en/principles.md), [values](templates/en/values.md), [roster](templates/en/members.md), [decisions](templates/en/decisions.md), [opportunities](templates/en/opportunities.md), [handoff](templates/en/handoff.md), and [approval table](templates/en/approvals.md). Use the [task brief](templates/en/task-brief.md) for files in `fleet/task-briefs/`. When the same kind of work recurs, use the [role card](templates/en/role-card.md) to create a role in `fleet/role-cards/`; do not invent roles just to fill an organization chart.

Keep the project card to one page: goal, rules, work in progress, decisions awaiting the reader, and a file map. Only the project manager role maintains it; other roles propose changes through handoffs and the decision log. Treat compacted summaries as clues and verify the matching handoff and actual state. If the intended recipient is unclear, explain which handoff needs matching and why, ask the reader here which role or task it belongs to, then verify that match before continuing. Record important decisions, new rules and newly started background tasks as they happen and reconcile them at closeout. Agree on a day to review the opportunity log each week and record which small trials the roles may run under the approval table.

Fill the files through the conversation instead of leaving forms for the reader to complete alone. Fill in the role roster in `fleet/members.md` using all seven columns: `Role`, `Department`, `Reports to`, `Assigned`, `How to call`, `Does`, `Delivers to`. Under “Does”, name the responsibility and the deliverable. Under “Delivers to”, give its file path. Under “Reports to”, use another role in the roster or “Human”. In the same file’s model assignment table, record the executing role, model and provider, and program or invocation method for each kind of work. Under “Accepted by”, identify who accepts it and whose review they rely on. Keep roles separate from models. Mark unverified invocation methods as such. With only one provider, mark the second “Not connected yet” and keep independent cross-provider review pending.

In `fleet/approvals.md`, list each matter and who can decide it. Spell out which decisions stay with the human, including direction, external commitments, spending and deletion. Roles decide the matters delegated to them. The approval table lists only matters and their decision makers.

Principles contain only confirmed working rules. Before adding a rule, ask whether it means fewer or more interruptions for the human; if more, look for another way first. Before removing one, find out what it guards against. Leave the values template empty until [fleet-values](../fleet-values/SKILL.md) helps the reader discuss their own values. The human sets direction. For rules about how to put it into practice, use the decision maker named in the approval table. Do not prefill an existing set of values. Keep proposed trials in the opportunity log. Put matters reserved for the human by the approval table under “Decisions needed”.

When you return the completed roster, distinguish the rules the project already had from any defaults introduced by the handbook templates during setup. Show what each role does and delivers, who it reports to and who checks its work, then list the decisions the human keeps. Next use [fleet-chart](../fleet-chart/SKILL.md) to generate `fleet/chart.md` from that roster, with the human inside the fleet. Check every reporting line against the roster and show the reader the chart in the same reply. Check the root card, import line, preserved original content, `fleet/` file map and delivery paths. Explain that when someone takes over later or resumes work in a new session, they should first read the root `AGENTS.md` and the latest handoff addressed to them, then check the actual deliverable to see what can continue, why and what must wait.

Ask the reader to pick one decision they need to make now for the fleet’s first task. Don't pick it for them; any example should be a decision in their project, not a practice task. Use [fleet-taskbook](../fleet-taskbook/SKILL.md) to write the first brief and show it to the human. Identify the sources, dependencies, author, deliverable and reviewer. State the acceptance and return conditions and which step needs the human’s decision. Check requested changes against the goal, revise the brief and get the human’s go-ahead before dispatch.

Before dispatch, reuse confirmed answers and tools along with tested invocation methods, without repeating completed steps. Ask only for missing details, one at a time: first which AI tools they have, then which models and providers those tools use, then how each is called—the exact command, or the three environment-variable names for the API address, key, and model, never their secret values. Explain that this identifies who can receive work, check each answer, and ask only for the next missing detail. Before testing each invocation method, explain that a small request may cost money and obtain the reader's agreement; then make a harmless request such as “Reply only OK”. After it succeeds, record the verified method and test date in the roster. If it fails, follow [fleet-dispatch](../fleet-dispatch/SKILL.md) to diagnose it and guide any needed installation, login or variable setup: explain the missing item and why it is needed, give one step at a time with the location and an exact command when available, and say whether to reply “done” or paste a non-secret result. Check the reply with a read-only check before continuing; if it still fails, give the next step without restarting the questions. Never ask for keys or passwords, or have the reader paste them into the conversation.

Dispatch the brief, collect the deliverable and have a peer check it. If a second provider is available, use fleet-dispatch to connect it and give a model from a different provider than the author the sources, deliverable and acceptance criteria for independent review. If the second provider is still unavailable, continue the work and keep independent cross-provider review pending. Report execution, receipt and review separately, then write a handoff for the next role. If the scope changes, keep the old criteria in the decision log and tell affected roles what to replan. Get the human’s agreement before any specific action that sets direction, makes an external commitment, spends money or deletes something.

## Report an issue to the AI Fleet Handbook

Use this flow for requests such as “Send a suggestion to the AI Fleet Handbook” or “Report a problem to the AI Fleet Handbook”. Do not submit anything just because the human asked for a draft.

1. Draft a title and Markdown body in the human's language. Use the matching fields below as headings; the installed skill does not need the repository's template files. For a bug, include the smallest reproduction steps, tools and versions, operating system, and Python version if used. Ask for missing essentials, and mark unknowns rather than inventing them. Leave optional fields out when there is nothing to add.

   | Kind | Template file | Fields (URL ID: meaning) |
   | --- | --- | --- |
   | Bug | `bug.yml` | `environment`: tools and versions, operating system, Python; `readme-step`: README step and command; `actual-and-expected`: what happened and what was expected; `reproduce`: smallest reproduction steps |
   | Stuck | `stuck.yml` | `stuck-step`: README or skill step; `stuck-goal`: what they wanted to do; `attempts`: what they tried; `time-spent`: how long, optional. Nothing has to be broken. |
   | Feature or idea | `feature.yml` | `feature-goal`: what they want to get done; `current-way`: current approach and inconvenience; `wanted-help`: how the handbook or skills should help. No implementation plan needed. |
   | Show your fleet | `show-your-fleet.yml` | `rule`: a rule on its own, with when it applies, how it was tried, and the result or a counterexample. All fleet details are optional. `organization-chart`: full mermaid code block from `fleet/chart.md` or `fleet/组织图.md`, keeping both fence lines, or a screenshot; `fleet-work`: what the fleet does; `provider-count`: number of model providers; `human-decisions`: decisions kept by the human; `extra-images`: optional self-check card or board screenshots |

2. Remove keys, tokens, account details, local machine paths, personal names, and material the human does not want public. Replace local paths with placeholders such as `<project-directory>`. Paraphrase confidential project material; do not paste whole passages. Check log excerpts and screenshots for sensitive details too. Keep the public repository address used below. Check the full draft before showing it.
3. Explain that the issue will be public. Show the exact title and full body first and say how it would be submitted (with the human's logged-in `gh`, or as a prefilled link they open and submit themselves; give the link if they prefer to submit it themselves), then ask “Submit this?” and wait for explicit approval. If the human edits anything, apply the edits, show the full title and body again, and get approval for that revision. Approval to draft is not approval to publish.
4. Only after approval, check whether `gh` is available and `gh auth status --hostname github.com` succeeds. If so, write only the approved, sanitized Markdown body to a new UTF-8 file in the project's temporary directory. Pass the approved title as one argument without shell interpolation and run `gh issue create --repo chenyuwang166-oss/fleet-handbook --title "<approved-title>" --body-file "<temporary-body-file>"`, then return the issue link. The command submits the body as written; it does not add template headings. If the result is unclear, check whether an issue was created before retrying.

   If `gh` is missing or not logged in, give the human a prefilled link to submit themselves: `https://github.com/chenyuwang166-oss/fleet-handbook/issues/new?template=<template-file>&title=<title>&<field-id>=<value>`. Use the template filename and the exact IDs from the table. Encode the parameter values, including newlines, with `urllib.parse.urlencode` or equivalent; do not encode the whole URL. If the URL is too long, prefill only the title and the most useful fields, and give the remaining approved text for the human to paste. Do not open or submit it automatically. Upload screenshots through the web form; a local image path cannot upload an image. If needed, offer the web form for a report with images.
5. Without explicit approval, do not submit. The human decides whether to file the issue and what it says. If they decline, stop; do not treat silence as approval.
