---
name: fleet-rounds
description: Experimental. Use to set up recurring project checks or advance agreed work on a wake-up. Distinguish host reminders, task pickup, and actual progress, and flag repeated stalls for review.
---

# Wake up and move the fleet forward one round

**Experimental.** We are trying this in our own fleet. On each round, check whether the host sent a reminder, who picked up the task, and what progress the files, deliveries, or reviews show. Record repeated nudges and stalls for the next retrospective. Scheduled reminders require a scheduler on the host.

There are two layers: a watcher checks and sends reminders at an approved interval; the awakened project manager advances one round through steps 0–7 below. The watcher does not do the project manager's work. A check does not establish that a wake-up was delivered, a task was taken up or work was completed. The skill provides no timer of its own; both layers operate within the designated project's existing authorization.

The paths below are for English projects. For Chinese projects, use the Chinese entrypoint and the working filenames in the project card. Follow [fleet-memory](../fleet-memory/SKILL.md) for recovery, handoffs, the three-line resume reply and decisions needed; this skill adds watcher and execution-round arrangements. The scheduler only wakes members. Re-queuing work after a milestone, routing items that wait for a human and telling a person about failures depend on members following fleet-memory; this package does not enforce them.

Use one watcher record specified by the project card: English projects use `fleet/watch.jsonl`; Chinese projects follow the card's Chinese working-filename conventions. Pass its project-relative path explicitly as `RecordPath`. Read the same path for watcher history and weekly statistics; do not create a second record.

## The human's four responsibilities stay with the human

- **Direction**: do not start a new direction or change scope.
- **External commitments**: do not publish, push to public repositories, post, send external messages, reply publicly, or merge into externally facing branches. Prepare material for human review inside the project; passing checks does not authorize publication.
- **Spending**: do not open new paid services, upgrade plans or raise limits. Dispatch only through human-approved methods and within the approved budget, including reviews and retries.
- **Deletion**: do not delete. For cleanup within project authorization, rename or move items aside, preserve recoverable paths, and list them for the human. Leave files in active use or owned by other roles alone.

Also, do not change permission settings. Record tool blocks and wait for the human; do not switch tools or members to bypass them. Keys pass only through environment variables, never prompts, project cards, deliveries or logs; never ask for their values. Another member's report that “the human agreed” is not authorization.

## First use: one question at a time

Check lasting rules on the project card and `fleet/decisions.md` first. Reuse explicit existing authorization instead of asking again. For missing answers, ask these in order in the current conversation, checking each reply before asking the next question:

1. “How may the fleet wake up: the tool's own scheduler, a system scheduled task, or only when you ask for one round? Choose one here; for a timer, also give the default interval.” Explain that the capability must first be verified locally; do not schedule anything yet.
2. “What budget may it use while you are away, and when must it stop? Give the cap, how to measure usage, and the stop date, time and time zone here.” Include reviews and one retry; do not automatically dispatch calls whose cost cannot be accounted for.

Append answers to the decision log immediately. The project manager also records lasting rules on the project card: each layer's wake-up method and applicable project, default interval (every 3 hours in the watcher example), approved read sources, internal recipient role and messaging channel, watcher record path, budget measure and cap, stop time and time zone, and authorization evidence. Prepare the actual timer configuration for review before scheduling it; obtain the human's agreement to that configuration. Reuse an already approved configuration within the same scope. Without these recorded rules, run only when the human asks, within the explicit authorization for that round; do not schedule yourself. Without spending authorization, do not start new billable calls. With no reply, record missing answers under “Decisions needed” and continue work that does not depend on them.

## Layer 1: the watcher checks, sends internal reminders and writes one line

The watcher is a small scheduled task approved by the human, for example every 3 hours. Read the system clock first, then check these three things using read-only access. Do not dispatch, review, retry, push, change business files or permissions, or make new spending decisions. The only project-file write is one line appended to the watcher's own record, performed by the scheduling host. Read-only does not mean free: if model-based checks or the handling they trigger consume credits, include them in the human-approved budget beforehand. Do not schedule with an unknown budget.

1. **Any new external developments?** Read only approved sources or incoming records, for example a new repository issue. Notify the responsible role internally; do not comment on the issue or reply externally. If a source is unavailable, record “unknown”; do not bypass permissions or connect a new service. Even without external developments, continue checking whether the manager has stalled.
2. **Has the project manager stalled?** Check host running state, substantive changes to deliveries and task logs, and the queue in the project card and handoff. Send an internal reminder only with evidence for all three conditions: **the manager is not running, more than one interval has elapsed since the last substantive activity, and work remains queued**. The reminder is: **“Take the next task from ‘Next steps’ on the project card; if the first task is blocked, start one that does not depend on it.”** Heartbeats, a growing watcher log or repeated replies saying “waiting” are not substantive progress. Record “unknown” when evidence is insufficient; silence alone does not prove a stall. The first task waiting for a human decision does not make the whole queue empty.
3. **Were this check and its wake-up result recorded?** Append one line per check to the record specified by the project card and passed as `RecordPath`. Include at least check time and time zone, whether the manager is running, the time and evidence of the last substantive activity, hours stalled, whether work remains queued, and whether a wake-up was sent. Also include check, stall-event and wake-up identifiers, message delivery state and its reason for deduplication and weekly statistics. Record failed checks too; do not turn “unknown” into “not running”.

Send only to the manager or responsible role through a human-approved internal channel. Pass on the lead; do not change the queue for the receiving role. With no channel or a failed send, leave the recipient role and exact pending message in the watcher's own run output and record “not delivered”. Do not claim a wake-up occurred or change the project card or handoff. A queued message does not prove the manager has taken up work; check actual actions on the next check before recording the effect. On receipt, the manager takes the next task already in the queue before looking for external developments.

Example record format (one line per entry; do not use these ellipses as actual evidence; the scheduling examples use the corresponding JSON fields):

```text
time=… | running=yes/no/unknown | last substantive activity=… | evidence=… | hours stalled=…/unknown | queued work=… | check ID=… | stall event=… | wake-up ID=…/none | wake-up=delivered/queued only/not delivered/not needed | message=… | previous wake-up effect=next task taken up/waiting only/awaiting observation/other reason (with evidence) | human status query=…/none/unknown
```

When an event occurs, use these record structures: `idle_event={id,started_at,evidence}`, `previous_wake={wake_id,outcome,observed_at,evidence}`, and `human_queries=[{event_id,at,source}]`. Times must include a time zone and hours stalled must be a nonnegative number. Leave absent events empty and mark missing evidence unknown. Do not use fields without identifiers, times or evidence in weekly counts.

Reuse the stall-event identifier while the same stall continues; each check is not a new stall. Close the event only when the manager resumes substantive activity. Deduplicate repeated triggers before sending; do not send the same wake-up twice. Check queue contents separately from which work is actionable now. If the same stall has already been reported, everything awaits human input, and no conditions or evidence have changed, continue recording facts and pending messages without starting the same billable execution round again. The manager must still follow fleet-memory to record why nothing can be done and when to check again, and propose one or two items to the human for a decision; do not erase the stall from the statistics. Reading external material, writing watcher records and queuing internal messages are each limited by their approved scope. Do not enable automatic execution unless a single instance and no duplicate dispatches can be assured. When the watcher ends, it does not execute the dispatch steps below.

## Layer 2: one round after a wake-up (steps 0–7)

### 0. Read the clock and the state

First read the system time and time zone, for example with PowerShell's `Get-Date -Format 'yyyy-MM-ddTHH:mm:sszzz'`, or `date '+%Y-%m-%dT%H:%M:%S%z'` in a terminal. Check the clock again before recording times, dispatching or scheduling; do not estimate dates from the conversation.

Follow [fleet-memory](../fleet-memory/SKILL.md): read running work, agreed next work, decisions needed and lasting rules in root `AGENTS.md`; read the latest section of `fleet/handoff.md` addressed to the project manager and this task, then `fleet/members.md`, `fleet/decisions.md`, approvals, relevant briefs and the existing board. If there is no board, note that and generate one later. Read only this project's files; do not borrow authorization from another project.

Check whether another round is running and whether each task has already been dispatched or reviewed. Duplicate wake-ups must not duplicate dispatches or schedules. If uncertain, verify first and preserve the state. Record the round identifier, start time and receiving role in the handoff. Check and record watcher-check, wake-up, round and call identifiers separately; do not combine them into one success. On waking, take up the next queued item before checking for outside activity. Carry forward each call's identifier, brief version, provider and tier, start and expected end times, timeout and stall criteria, output and log paths, retry count, consecutive occurrences of the same error, and spent, reserved and remaining budget. Missing evidence does not mean zero usage.

### 1. Check running work

For each task, check delivery files, the first-line completion status, completeness of content, the log tail and last modification time, then the host task or process state. **Exit code 0 can still accompany an interrupted result; receiving a file does not establish quality.**

| State | Evidence and next action |
| --- | --- |
| Run ended | Content and completion status are complete, with no unresolved interruption in the log. Record whether execution ended and what was received, then move to step 2. If the exit state is unavailable, mark insufficient evidence rather than claiming verification. |
| Still running | Verifiable running-state evidence and progress consistent with expectations. Record the evidence and expected end time; do not redispatch. Some tools emit results only at the end, so a quiet log alone does not establish a stall. |
| Stalled | Far beyond the expected time agreed in the brief, with no log growth, and host evidence that rules out normal silence. Record observation time, how overdue it is, and the last activity. Without agreed criteria or enough evidence, mark a suspected stall and investigate first. |
| Failed | Nonzero exit, empty content, missing completion status, incomplete delivery, or an interruption in the log. Record the cause and original evidence, not just the exit code. |

Retry once with a new identifier only when the failure is clearly transient, such as a busy provider, the brief is unchanged, the old call has definitely ended, and budget and stop time allow it. Preserve old outputs and logs. Check for an existing result first; do not repeat an action whose outcome is unknown. For other failures, record a decision needed, or revise and version the brief under [fleet-taskbook](../fleet-taskbook/SKILL.md) only within existing scope and authorization before redispatching. Rewording a brief does not reset failure counts. Two consecutive occurrences of the same error stop automatic progress for this round; preserve evidence for the human.

A timeout does not prove a process has stopped. Until the old call is confirmed ended, do not retry or start potentially conflicting calls; do not kill it without authorization. Follow [fleet-dispatch](../fleet-dispatch/SKILL.md). By default, leave no unattended background tasks at session end. Tasks may span rounds only with human authorization and a host that can reliably manage them and expose verifiable state and a way to resume; record these conditions immediately. Otherwise, do not start new background tasks.

### 2. Collect deliveries and arrange review

Follow fleet-dispatch: select a reviewer from a **different model provider than the author**, at a **tier no lower than the writing call**. Tool names do not establish providers. Use verified, comparable configurations in the roster or an explicit human assignment to establish tiers; do not infer rank from model names. If a suitable member or tier evidence is missing, mark independent review as pending with the specific gap. Do not lower the tier to fill the role; prepare review inputs meanwhile.

Give the reviewer original sources, the delivery, acceptance criteria from the brief and available execution evidence, not only the author's self-assessment. For API members without file access, include the necessary source text in the brief under fleet-taskbook. Record reviewer provider, tier, report path, verdict, issues and their resolution. Return failed work to the author within the original scope, saving a new version, then obtain another review from a different provider at the required tier. Preserve old drafts; repeated revisions must not bypass budget, stop time or the two-error limit. The project manager closes the task against acceptance criteria; authors do not accept their own work. Explain any issues left unchanged. Record execution ended, delivery received, review passed, human acceptance and publication permission separately.

### 3. Dispatch the next agreed task

Select only next work already agreed on the project card. Check prerequisite deliveries and reviews, the brief, roster invocation, writable scope, remaining budget and stop time. Reserve budget for both writing and review, with retries also charged against the balance. Do not dispatch unless the total can be confirmed within the cap. Do not start new directions, expand scope, or treat a proposal under “Decisions needed” as an agreed task. Continue independent authorized work while pausing the blocked branch.

### 4. Update the board

First reconcile verified running work, next work and review state in the project card and handoff; predictions are not progress. Follow [fleet-chart](../fleet-chart/SKILL.md), running from the skill bundle root with the actual project path:

```bash
python -X utf8 skills/fleet-chart/scripts/fleet_board.py --project <project-directory>
```

Open the generated `fleet/board.html` and check its “Needs you”, “Work in progress”, “Done” and “Roster” sections. Unreviewed work and failed reviews must not count as done. If the generator cannot interpret this round's records, document the discrepancy in the handoff instead of reporting incorrect counts as facts. State when rendered output has not actually been viewed. Read an existing status line if available; do not configure a new one or change settings here. If steps 5–7 change the records, regenerate before ending.

### 5. Batch decisions for the human

Follow [fleet-memory](../fleet-memory/SKILL.md) for decisions needed: other roles propose them through handoffs, and the project manager records them on the project card. Batch items that do not block current work; ask immediately about an item that blocks work. While waiting for the reply, set that item aside and continue with work it does not block, so the whole line does not wait with it. While the human is away, do not repeatedly ask or decide on their behalf; record which question comes next. First-time wake-up and budget setup still proceeds one question at a time. Follow fleet-memory for recording verified decisions and updating pending items too.

When reminders or stalls recur, add the task and its evidence location to the opportunity log as they happen. Apply the trial criteria in fleet-memory. Include a retrospective proposal and its evidence in this batch when it needs a human decision. Trials within existing authority can proceed under that authority.

### 6. Write a handoff

Follow [fleet-memory](../fleet-memory/SKILL.md) and the [handoff template](../fleet-setup/templates/en/handoff.md) to append a section to `fleet/handoff.md`, naming the receiving role and task in its heading and reading the system clock before recording dates and times. Use that skill and template for shared fields, judgment labels, resume commands and verification after reopening. Add this round's call and review evidence, failure counts, budget usage and reservations, running tasks and how the host manages them, and the reason for stopping.

For “Next steps”, follow the template: “The queued items in order, past any milestone; which one to do first if the first item is blocked, for example by a pending human decision.” Keep the queue within the agreed scope. After a review, delivery or scheduled milestone, state what follows a pass, a failure or work still running. If nothing can be done, write in the handoff why and when to check again. As project manager, tell the human and put one or two proposed next items in the next batch of “Decisions needed”; they are not agreed work until confirmed. Without an approved interval or credible event time, give a specific proposed time and the missing authorization rather than claiming a reservation exists. At a hard stop, record who can lift it and when it will be reviewed; do not continue on your own.

At the end of every round, check these three questions and put the answers in the handoff:

- What is the first item under “Next steps” when this round ends, who does it and when?
- Is work queued for after the milestone?
- Is the item waiting for a human under “Decisions needed”? Has other work continued? If nothing can proceed, are the reason, recheck time and one or two proposed items for the human recorded?

When the human asks to continue, verify actual state and give fleet-memory's three-line resume reply. The first sentence afterward reports this round's review issues, which were fixed, which were not and why; say if review has not happened. This also preserves fleet-dispatch's review-first reporting convention.

### 7. Choose the next wake-up time

Read the clock again. Prefer when the next event needs attention, such as a running task's expected end. Use the human's default interval only when there is no specific event time. Record an absolute date, time, time zone and reason, within the stop time. When nothing can proceed, follow step 6 to record why, when to check again and proposed items for the human; a recheck is not new execution authorization. The watcher's fallback check every 3 hours does not replace the manager's next round scheduled around task events.

Schedule only through a human-approved method verified available on this machine. Reuse an existing periodic trigger if it covers the need; do not stack schedules. Record the schedule identifier, trigger time, working directory and prompt location, and verify that scheduling succeeded. Record failures instead of claiming an automatic wake-up. Without an approved method, without a default interval or event time, or with uncertain configuration, record only a proposed time, or “undetermined” if none can be inferred, and the missing conditions, then stop here.

## When to stop

Any of these stops new dispatches and further automatic execution in the execution layer. Collect results that can safely be collected and write a handoff with the specific reason:

- No running work and no agreed next task. Unreviewed deliveries are still pending work; process them first or record the blocker.
- The same error has occurred twice consecutively, counting across rounds and restarts.
- Another dispatch, including review or retry, would exceed the human's budget, or the remaining balance cannot be verified.
- The human's stop time has arrived.

Do not interpret “no next task” as a global stop when the first task awaits a human but a second task can proceed. Apply the existing authorization to each layer's deadline and budget; if an older authorization gives only one stop time, it applies to both layers by default. Watching after execution stops needs separately approved watcher limits, deadline and reminder scope; read-only status does not authorize an extension. Stopping new actions does not authorize terminating running tasks. Follow their approved host-management and stopping rules, recording who takes over and how to check. Do not kill processes or extend budget or stop time without authorization. An existing periodic trigger after the deadline may only verify and exit; waking again does not reopen dispatch authorization. Disable future triggers within the human-approved schedule-management scope; if that cannot be done, record it for the human.

## Copyable wake-up prompt

Fill in the verified project path and run from that root. Chinese projects use the Chinese prompt and working filenames.

```text
Wake up and run one fleet-rounds round. The project is <project-directory>. First read the system clock, project card, latest handoff addressed to the project manager and this task, roster and board, and verify actual deliveries and logs. On waking, take up the next queued item before checking for outside activity; if the first task awaits a human, start one that does not depend on it. Check running work, collect deliveries and arrange review by a different provider at a tier no lower than the author. Advance only agreed, authorized next work within budget. Record human decisions needed, update the board and append a handoff following fleet-memory and the handoff template. Queue the next several tasks in order beyond the next milestone, specifying which task to do first if the first one is blocked. If nothing can be done, record why and when to check again, tell the human and put one or two proposed next items in the next batch of “Decisions needed”; they are not agreed work until confirmed. Choose the next wake-up under existing authorization. Do not start new directions or change scope, make external commitments, add spending or delete anything, change permissions, or bypass tool blocks. Without authorization, record a proposal only; do not schedule. If a stopping condition applies, record why and stop. Do not duplicate dispatches.
```

## Weekly check: is self-directed progress working?

Using the project's agreed time zone and week boundaries, the project manager checks these three measures against watcher records and “Decisions needed”. Statistics are the manager's job; the watcher reads and records facts. To make the counts possible, when the human asks “Are you still working?”, the manager records the message time and evidence in the relevant pending-decision item or handoff. On its next check, the watcher copies that event into its own single-line record. Actual actions following a wake-up are linked to the original wake-up identifier in later watcher entries. With missing history or no check coverage, record “unknown”, not zero.

1. **How many times the human had to ask “Are you still working?” Target: 0.** Quoting or forwarding the same human message does not add to the count; a genuinely repeated question does. If it is unclear whether a message was a necessary status query, flag it for judgment rather than hiding it.
2. **How many stalls lasted more than one interval while work was queued?** Count continuous stall events: count once on first crossing the interval, then update duration on later checks. A new event begins only if substantive activity resumes and then stalls again. List ongoing events from a previous week separately as “unresolved”; do not recount them each week. Record reasons such as waiting for a human or a pause required by authorization, without silently excluding them.
3. **After each wake-up, was the next task taken up, or did the manager only reply “waiting”?** List each unique wake-up identifier as “next task taken up / waiting only / awaiting observation / not delivered / other reason”, with task or call evidence. Sending a message, successfully queuing one or receiving a reply is not evidence that work was taken up. Explain nondelivery and cases such as reaching the stop time separately; do not invent a success rate.

Use these measures to find gaps in the queue, authorization or wake-up channel. They do not automatically expand permissions or budget, and more frequent checks must not inflate stall counts.

## Scheduling the watcher: pattern and two tool examples

Prepare a watcher prompt inside the project, such as `fleet/watch-prompt.txt`, separate from the execution-round prompt above:

```text
This is a fleet-rounds watcher check, not an execution round. Within the approved scope, use read-only access to check for external developments and pass them to the responsible role; check whether the project manager is not running, more than one interval has elapsed since the last substantive activity, and work remains queued. The example interval is 3 hours; use the approved rules. When the conditions hold, send only an internal reminder: “Take the next task from ‘Next steps’ on the project card; if the first task is blocked, start one that does not depend on it.” Do not dispatch, review, spend, push, or change other files or permissions. Append only one line to your own watcher record, with persistence handled by the scheduling host. Output single-line JSON and no other prose. Include at least running (yes/no/unknown), last_activity (a timestamp with time zone or null), idle_hours (a number or null), queued (yes/no/unknown), wake (not-needed/unsent), and message (the recipient role and exact pending message). When supported by evidence, also include last_activity_evidence, idle_event, previous_wake and human_queries; the host adds check_time and a check identifier. Without state evidence, record unknown. With no approved messaging channel or a failed send, leave only the recipient role and exact pending message in this run's output and record not delivered. Do not reset the manager's last substantive activity time because this record grew. Do not execute steps 0–7.
```

Use the evidence categories in the installation guide's [verified invocation methods](https://github.com/chenyuwang166-oss/fleet-handbook/blob/main/install/README.md#verified-invocation-methods):

| Category | Evidence and scope |
| --- | --- |
| Runs we completed | The installation guide records a run of `codex exec --skip-git-repo-check "<prompt>"` with Codex CLI 0.160.0 on 2026-11-15. Evidence for the earlier MetaCode invocation remains in that guide; this section adds no usage for that tool. |
| From documentation or visible in local help; no actual invocation | On 2026-11-18, local Claude Code 2.1.169 help for `claude --help` listed `-p`, `--tools` and `--permission-mode dontAsk`. Codex CLI 0.160.0 help for `codex --help` and `codex exec --help` listed the example parameters `exec`, `-s read-only` and `--skip-git-repo-check`. |
| Internal messaging, visible in help only | On the same date, `codex queue --help` listed `--thread <THREAD> --message <TEXT>` to queue a message for an existing session. Processing or taking up work was not verified. The checked Claude help did not document a comparable messaging command, so no Claude messaging example is provided. |
| Scheduling chain not run | The help checked did not show a native scheduling entrypoint sufficient to write an example. This does not prove the tools have no scheduling capability. Both examples below use system scheduling; command parameters were checked only against local help, and the full chain has not been run. |

**Show the actual configuration to the human and obtain approval before scheduling either example.** Include project and tool absolute paths, time zone, the 3-hour interval, both layers' deadlines and budgets, read-only state sources, internal recipient role, record file and overlap prevention. Scheduling is complete only after current running state, last substantive activity, message delivery and single-line recording are verified on the target host. If any is missing, leave a proposal first. Read-only tool parameters do not guarantee zero charges or prove that all plugins, hooks and messaging channels are isolated. Preserve existing permissions; do not automatically elevate privileges or bypass blocks.

### Example 1: a read-only Claude Code watcher entrypoint

```powershell
& 'C:\path\to\claude.exe' -p $watchPrompt --permission-mode dontAsk --tools 'Read,Glob,Grep'
```

Only project-file reading tools are available here, with no shell, editing, dispatch or message-delivery tools. The host must supply current read-only evidence of running state; record “unknown” when it is missing. Leave the exact pending message in the run output for an approved host channel to forward. `--tools` restricts tools; do not describe it as a fully verified read-only sandbox. Do not schedule until plugin and hook boundaries have been verified.

### Example 2: a read-only Codex watcher entrypoint

```powershell
& 'C:\path\to\codex.cmd' exec -s read-only --skip-git-repo-check $watchPrompt
```

`read-only` constrains the model's project writes; the outer host appends watcher records. Without an approved messaging channel, leave only the pending message here too. An optional internal-queue template is `codex queue --thread "$env:FLEET_PM_TARGET" --message '<exact-pending-message>'`; the human chooses the target, so no specific session identifier is given. Messaging needs its own internal-messaging authorization and must be performed by the host, without bypassing the read-only sandbox. Until processing is verified, record only “queued”. Neither read-only entrypoint calls this messaging command.

### Scheduling both examples: Windows system scheduling every 3 hours

**The outer scheduling host must enforce these gates before starting; a prompt cannot repair them afterward:** check the watcher deadline and budget across rounds, reserve this check's allowance, and apply a finite human-approved timeout to the model call. Even if the model hangs or a child process times out, the host must append a failure record. Do not restart overlapping checks before verifying the old call's state; terminate only under the human-approved rules for that read-only child process. The single-run logging example below does not implement budget or timeout management across rounds and must not be deployed alone. Until these gates, state sources and messaging channels are connected, do not register a timer or claim unattended progress works.

The following is example content for `fleet/watch.ps1`, shared by both entrypoints. Replace the paths; the entrypoint must already be installed and the project's `fleet/` directory must exist. The `ToolChoice` argument, `claude` or `codex`, only selects which approved tool to use. The required `RecordPath` must be the project-relative record path specified by the project card. This example passes `fleet/watch.jsonl`; existing projects use their approved path. The script appends only its own record, leaving command results in host stdout and appending an “unknown” record on failure. It does not send messages or dispatch work. For automatic message forwarding, connect a separately approved and verified host channel; do not treat this example's “not delivered” as a successful wake-up.

```powershell
param(
    [Parameter(Mandatory=$true)][ValidateSet('claude','codex')][string]$ToolChoice,
    [Parameter(Mandatory=$true)][string]$RecordPath
)
$ErrorActionPreference = 'Stop'
Set-Location -LiteralPath 'C:\path\to\project'
if ([string]::IsNullOrWhiteSpace($RecordPath) -or [System.IO.Path]::IsPathRooted($RecordPath)) { throw 'RecordPath must be project-relative' }
$watchRootPrefix = (Get-Location).Path.TrimEnd([System.IO.Path]::DirectorySeparatorChar) + [System.IO.Path]::DirectorySeparatorChar
$watchRecordPath = [System.IO.Path]::GetFullPath((Join-Path (Get-Location).Path $RecordPath))
if (-not $watchRecordPath.StartsWith($watchRootPrefix, [System.StringComparison]::Ordinal)) { throw 'RecordPath must stay inside the project' }
$watchTime = Get-Date -Format 'yyyy-MM-ddTHH:mm:sszzz'
$watchRecord = [ordered]@{check_time=$watchTime; check_id=[guid]::NewGuid().ToString(); running='unknown'; last_activity=$null; idle_hours=$null; queued='unknown'; wake='unsent'; message='Check failed; inspect the output of this run'; check_status='failed'}
$watchExit = 1
try {
    $watchPrompt = Get-Content -Raw -Encoding utf8 -LiteralPath 'fleet/watch-prompt.txt'
    if ([string]::IsNullOrWhiteSpace($watchPrompt)) { throw 'Missing prompt' }
    $watchPrompt = "Check time: $watchTime`nWatcher record: $RecordPath (use this same path for history and appends)`n" + $watchPrompt
    if ($ToolChoice -eq 'claude') {
        $watchOutput = @(& 'C:\path\to\claude.exe' -p $watchPrompt --permission-mode dontAsk --tools 'Read,Glob,Grep')
    } else {
        $watchOutput = @(& 'C:\path\to\codex.cmd' exec -s read-only --skip-git-repo-check $watchPrompt)
    }
    if ($LASTEXITCODE -ne 0 -or $watchOutput.Count -ne 1) { throw 'Check failed or output is not one line' }
    $watchData = $watchOutput[0] | ConvertFrom-Json -ErrorAction Stop
    foreach ($field in @('running','last_activity','idle_hours','queued','wake','message')) {
        if ($watchData.PSObject.Properties.Name -notcontains $field) { throw 'Missing record field' }
    }
    if ($watchData.running -notin @('yes','no','unknown') -or $watchData.queued -notin @('yes','no','unknown') -or $watchData.wake -notin @('not-needed','unsent')) { throw 'Invalid record state' }
    if ($null -ne $watchData.idle_hours -and ($watchData.idle_hours -is [string] -or $watchData.idle_hours -is [bool] -or $watchData.idle_hours -isnot [ValueType] -or [double]$watchData.idle_hours -lt 0)) { throw 'Invalid idle hours' }
    if ($null -ne $watchData.last_activity) {
        $watchActivity = [regex]::Match($watchOutput[0], '"last_activity"\s*:\s*"([^"\\]+(?:Z|[+-]\d{2}:\d{2}))"')
        if (-not $watchActivity.Success) { throw 'Activity time must include a time zone' }
        [datetimeoffset]::Parse($watchActivity.Groups[1].Value) | Out-Null
        $watchData.last_activity = $watchActivity.Groups[1].Value
    }
    foreach ($field in @('running','last_activity','idle_hours','queued','wake','message','last_activity_evidence','idle_event','previous_wake','human_queries')) {
        if ($watchData.PSObject.Properties.Name -contains $field) { $watchRecord[$field] = $watchData.$field }
    }
    $watchRecord.check_status = 'ok'
    $watchExit = 0
} catch {
    # Keep failure output in the host run output, outside project records.
    if ($watchOutput) { $watchOutput | Write-Output }
} finally {
    $watchLine = $watchRecord | ConvertTo-Json -Compress -Depth 8
    Add-Content -LiteralPath $watchRecordPath -Value $watchLine -Encoding utf8
    Write-Output $watchLine
}
exit $watchExit
```

The host preserves tool run output and stderr. If the record path itself is unwritable, the scheduled run must explicitly report persistence failure rather than claim the check completed. The watcher's own record time is not the manager's last substantive activity. A check identifier is not a stall or wake-up identifier.

For a manual English run, also pass the path explicitly, for example `./fleet/watch.ps1 -ToolChoice codex -RecordPath 'fleet/watch.jsonl'`. Set `$approvedToolChoice='claude'` for the Claude example, or `$approvedToolChoice='codex'` for Codex; both set `$approvedRecordPath='fleet/watch.jsonl'` (use the project card's path for an existing project). The following only prepares system configuration. `$approvedHostScript` must point to a verified, complete host entrypoint enforcing the gates above, not the single-run logging example. This example assumes the host accepts and forwards `ToolChoice` and `RecordPath`; the package does not provide that host script. Supply `$approvedFirstCheck`, `$approvedDuration` and `$approvedTaskName` from the human-approved first check time, repetition duration and unused task name; no specific name is given. The repetition duration must not extend beyond the watcher's deadline. Show the actual configuration to the human and run the registration line only after approval:

```powershell
$watchArgs = '-NoProfile -File "' + $approvedHostScript + '" -ToolChoice ' + $approvedToolChoice + ' -RecordPath "' + $approvedRecordPath + '"'
$watchAction = New-ScheduledTaskAction -Execute 'powershell.exe' -Argument $watchArgs -WorkingDirectory 'C:\path\to\project'
$watchTrigger = New-ScheduledTaskTrigger -Once -At $approvedFirstCheck -RepetitionInterval (New-TimeSpan -Hours 3) -RepetitionDuration $approvedDuration
$watchSettings = New-ScheduledTaskSettingsSet -MultipleInstances IgnoreNew
Register-ScheduledTask -TaskName $approvedTaskName -Action $watchAction -Trigger $watchTrigger -Settings $watchSettings
```

The full scheduling chain has not been run. It is usable only after state sources, single-line recording, budget and deadline, message results and overlap prevention have been verified on the target host. `IgnoreNew` covers only the same task; deduplicate manual checks and other tasks too. Do not automatically save passwords, relax execution policy, elevate privileges or enable services. Record blocks and wait for the human. A template here is not permission to register a task automatically.

### cron example: trigger the approved host entrypoint every 3 hours

**This example has not been run. Obtain the human's agreement before scheduling it.** This shows only how cron invokes the host: the target machine must already have `pwsh`. Replace `/path/to/approved-host.ps1` with a verified, complete host entrypoint enforcing budget reservations, the deadline, a finite timeout, failure records and overlap prevention. It accepts and forwards `ToolChoice` and `RecordPath`, then calls the single-run logging script. Do not substitute the `fleet/watch.ps1` logging example for the host entrypoint. Do not install dependencies for this example. Replace the script's Windows project and tool paths with verified paths on the target machine. The approved host still manages message results and run output.

```cron
0 */3 * * * cd /path/to/project && /path/to/pwsh -NoProfile -File /path/to/approved-host.ps1 -ToolChoice codex -RecordPath 'fleet/watch.jsonl'
```

This fires at 00:00, 03:00, 06:00 and so on in the cron host's time zone, rather than every 3 hours from registration. For Claude, replace `-ToolChoice codex` with `-ToolChoice claude`. In an existing project, use the card's `RecordPath`; do not redirect to a second project log. Do not assume waking from sleep, catch-up runs or overlap prevention. Whether a closed tool can be started also needs verification for the actual host. Until the outer host is ready, leave only a configuration proposal; do not schedule or claim this package already watches for you.
