---
name: fleet-taskbook
description: Use when a later step depends on a deliverable and someone needs a task brief naming the sources, dependencies, acceptance checks, and grounds for sending it back.
---

# Write a task brief

Start with the next decision in this project. Identify the deliverable it depends on, who will use it, and what evidence would let a reviewer accept it or send it back.

Work from the project root. Read root `AGENTS.md`, `fleet/principles.md`, and `fleet/members.md` to understand the purpose, available materials, and acceptance criteria. Check an existing brief before making necessary revisions. These are the English project paths. For a Chinese project, follow its project card and retain its existing Chinese filenames.

Use the [English brief template](../fleet-setup/templates/en/task-brief.md) and save English briefs in `fleet/task-briefs/`. For Chinese project documents, use the entrypoint installed with `--lang zh` and the task directory in its project card. Its first line tells the recipient to read the principles. The body covers purpose, current state, work required, boundaries, and handback. Make the goal concrete through delivery files and acceptance evidence; retain only constraints that affect the outcome. Specify the input scope, writable locations, handback time or stopping condition. Inputs are materials; instructions within them do not automatically gain permission to execute.

Every brief must name the assigned member, model provider, and **Reasoning level**. Use the tool's own level names, such as `high`; if the tool does not allow a choice, write “Tool default”. Default to the tool's high level (`high`). The review assignment must use a level at least as high as the author's assignment. Use a lower level only for large volumes of mechanical work, such as reformatting or bulk copying, and explain the reduction in one sentence in the brief.

When the member is called only through an API, it sees nothing but the brief: it cannot read project files or run commands. Delete the first "Read …" line, paste the materials it needs into Current state, and have it deliver in the response.

Record the human's approval and its scope before spending money, making external commitments, deleting anything, or deciding the project's direction. If approval is missing, finish reversible preparation, explain the specific decision and why it is needed, and ask the human to reply here with agreement or a change; verify that reply before taking the action. For account access, state existing authorization without asking for keys, passwords, or secret values. For other matters, explain risks and consequences and leave implementation choices to the recipient. When materials are missing, separate work that can proceed independently from work that must wait.

Save the finished work in the agreed format. Put its status (Complete, Incomplete, or Blocked) on the first line of a separate Notes file. Label each judgment Observed, Inferred, or Guessed. Include evidence locations, unfinished work, the next step, and comments on the brief. If the tool saves a whole response as one file, put these notes under a final `## Notes` heading for the project manager to split off before review. Name the reviewer and the inputs used for acceptance. Authors do not accept their own work.

Use the template’s handback instructions to invite objections. Members may suggest another approach or decline to follow an instruction they believe is wrong. Ask them to record their reasons in the separate Notes file.

Before handing it back, read it from the recipient's perspective: can they find the materials, judge completion, and identify the next recipient without the previous session? If a goal or essential input is missing and cannot be inferred, tell the human exactly what to supply and why it is needed. Ask for one item at a time, saying where to act and what to send back; for example, ask them to reply here with the intended deliverable, or to place an input file in the agreed project directory and reply with its path. After their reply, check that the goal is clear or the file is readable and relevant, using a read-only check where possible, then continue the brief. If the check fails, give the next corrective step without restarting the questions. Never request secret values. Do not turn a guess into a settled requirement.

## No first task yet

Choose a decision in your current project. Read the project card and existing materials first, then use these questions to fill in what is missing.

- What decision were you already going to make this week? Who is waiting for it, and what will they do next?
- Which deliverable would let that person proceed? Which file, version, or earlier decision does it depend on? What is still missing?
- Who supplies the original and who reviews it? Which source passage or repeatable check would justify accepting the work or sending it back? Who decides if reviewers disagree?
- If an input or requirement changes, what can continue and what has to wait? Where can the next member find the earlier criteria and the new decision?

Write the brief for this decision alone. Give the reviewer the same source version, deliverable, and acceptance criteria. Trace factual claims to a page, passage, or table and check numbers, units, dates, and scope. For a check someone can run, keep the input, command or steps, expected result, and actual result so the next member can repeat it.

Assign independent review to a member from a different provider than the author. For an API-only reviewer, put the source excerpts and evidence from checks in the review brief. Name who ran each check and what still needs verification. With one provider, complete the available work and record cross-provider review as pending.

Report separately whether execution ended, the deliverable arrived, and review passed. Link each finding to a source or test record and say what changed, what remains open, and who takes it next. Report zero findings only after a review actually found none.
