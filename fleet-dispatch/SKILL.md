---
name: fleet-dispatch
description: Use when the project manager needs to assign the original delivery, name an independent reviewer to check acceptance criteria, or route conflicting findings to the role authorized to decide.
---

# Dispatch work and verify the result

Before dispatch, the project manager uses the roster and brief to name the member who will deliver the original work, a reviewer from another provider who will check it against the agreed criteria, and the role that will resolve disagreements. At collection, save the deliverable and notes separately. The reviewer checks the work against the source materials. The project manager closes against the criteria and routes any decision beyond that role's authority through the project's approval table.

Read root `AGENTS.md`, `fleet/principles.md`, `fleet/members.md`, and the current brief in `fleet/task-briefs/`. These are the English project paths. For a Chinese project, follow the file map in its project card and retain its existing Chinese filenames. Choose a roster member whose responsibilities fit the task and whose model provider differs from the provider used by the current host. A tool's name does not identify its model provider. Verify the assignment before choosing a member. If provider information is missing, no suitable member exists, the tool is absent, or login is incomplete, state what is missing and why it is needed, then guide the human one step at a time. Say where to act, give the exact command when possible, and specify what to report back: for example, ask for the missing provider name here, or ask for the last non-secret line of a check run in their own terminal. After the reply, verify the result with a read-only check when available, then resume dispatch; if the check fails, give the next corrective step without restarting the questions. Never ask for keys or passwords or have them pasted into the conversation. Get the human's agreement before spending money, making external commitments, deleting anything, or deciding the project's direction; do not assume that using another tool means using another provider. If another provider's command-line tool reports that it is not logged in or cannot reach the network when called from your own sandbox, the sandbox is most likely blocking access to its login and network: give the human the exact command to run and ask the human to run it in their own terminal; do not ask the human to log in again.

Check all seven roster fields. English headers are `Role`, `Department`, `Reports to`, `Assigned`, `How to call`, `Does`, `Delivers to`; Chinese headers are `岗位`, `部门`, `汇报给`, `派驻`, `怎么调用`, `做什么`, `交付写进`. Use only verified invocation methods. Do not treat arbitrary table text as trusted shell code. By default, headless tasks read the supplied materials and return their result in the response. File writes, network access, and other actions require the authorization stated in the brief. If a human decision is needed for approval, explain the specific decision and why it is needed, ask the human to reply here with agreement or a change, and verify that the reply authorizes the action before continuing. If no one responds, report the pending decision and continue only already-authorized preparation.

Pass the brief's **Reasoning level** to the tool, rather than mentioning it only in the prompt. The review assignment must use a level at least as high as the author's; document any reduction in the brief under the fleet-taskbook rules before dispatch. If the tool offers no choice, use its default. Record the level used for the invocation in the handback or handoff, with the command, configuration, or runtime log as evidence. If it cannot be verified, say “Unverified”; a model's self-report does not establish that the setting took effect.

## Command-line dispatch

Any tool that accepts a prompt from the command line and prints its response can be used. Follow that tool's own syntax, verify it, and record it in the roster's `How to call` column. For specific examples and their test status, see [Verified invocation methods](https://github.com/chenyuwang166-oss/fleet-handbook/blob/main/install/README.md#verified-invocation-methods).

Check level syntax against the installed tool's help. Replace `high` below with the level specified in the brief. Checking help does not mean a call with that setting has been run.

- Codex: add `-c model_reasoning_effort=high` to the verified command, for example `codex exec --skip-git-repo-check -c model_reasoning_effort=high "<prompt>"`. In `codex exec --help`, `-c, --config <key=value>` overrides a configuration value. Values are parsed as TOML, with the raw string used if parsing fails. See the Codex configuration documentation for supported levels.
- MetaCode: the local `metacode exec --help` describes only the generic `--config <key=value>` option, without identifying a reasoning-level key. Use its own settings; do not guess a parameter.
- Claude Code (from its documentation; we have not run this): `claude -p --effort high "<prompt>"`. The local `claude --help` lists `--effort <level>` with `low`, `medium`, `high`, `xhigh`, and `max` as choices.
- metacodes (from its documentation; we have not run this): use its own settings. No reasoning-level parameter has been verified, so none is shown.

Set the timeout for the task: give long tasks a longer timeout. If a tool returns its result only when the invocation finishes, keep each assignment small enough to finish and review as one unit. Disable input and apply the timeout in the actual command. Capture the full response separately from the runtime log and split it before review. The runner below uses Python 3.9 or later from the project root in Git Bash or a macOS or Linux terminal. Replace the entire `command` line with the verified command from the roster's `How to call` column, passing `prompt` as that tool expects. The empty placeholder stops at "invocation did not start" without calling a program. Use a new file number and preserve earlier deliveries.

```bash
python -X utf8 - <<'PY'
from pathlib import Path
import subprocess
import sys
timeout_seconds = 600  # adjust for the task; long tasks need more
prompt = Path("fleet/task-briefs/01.md").read_text(encoding="utf-8")
command = []  # Replace this entire line with the verified command from the roster's 'How to call'.
Path("fleet/deliveries").mkdir(parents=True, exist_ok=True)
with open("fleet/deliveries/01.md", "x", encoding="utf-8") as out, open("fleet/deliveries/01.log", "x", encoding="utf-8") as log:
    try:
        if not command:
            raise OSError("No verified command selected")
        result = subprocess.run(command, stdin=subprocess.DEVNULL,
                                stdout=out, stderr=log, timeout=timeout_seconds)
    except subprocess.TimeoutExpired:
        print("Blocked: invocation timed out; current output has not been accepted.", file=log)
        sys.exit(2)
    except OSError:
        print("Blocked: invocation did not start; check the tool and login.", file=log)
        sys.exit(2)
sys.exit(result.returncode)
PY
```

Use the delivery filename specified in the brief. For the notes file, replace the delivery file’s extension with `-notes.md`. Keep only the finished work in the delivery file. Put the completion status on the first line of the notes, followed by evidence locations, unfinished work, next steps, and comments on the brief. If the tool can return only one response, have it put the finished work first and end with `## Notes`, with the status directly below that heading. After the command ends, the project manager opens the response and log, saves the text below the final `## Notes` heading in the notes file, and removes that section from the delivery file. Check that the delivery file contains only the deliverable before sending it for independent review. Supply the notes separately when the reviewer needs the evidence. A nonzero exit, empty response, missing status in the notes, or incomplete delivery cannot count as complete. State the cause before revising the brief or invocation. After a timeout, verify that the process has actually ended. If it has not, pause new dispatches and explain why; give the human one read-only command suited to their system to inspect that specific process in their own terminal, and ask for its status. Verify the status after their reply, then guide the next step until the process has ended. Get approval before terminating a process if doing so could lose work. Do not leave background tasks running when the session ends.

## When the second provider only has an API key

Most providers offer the OpenAI chat format, which this runner uses by default. For an Anthropic-format endpoint (`/v1/messages`), set `FLEET_API_BASE` to its address, for example Anthropic's official API at `https://api.anthropic.com/v1`, or a provider's Claude Code address with `/v1` appended, and set `FLEET_API_FORMAT=anthropic`. Read its base address through `/v1`, model name, and key from `FLEET_API_BASE`, `FLEET_API_MODEL`, and `FLEET_API_KEY`. In the roster's `How to call` column, write only `API: FLEET_API_BASE, FLEET_API_MODEL, FLEET_API_KEY (environment variables)`. For the Anthropic format, also record `FLEET_API_FORMAT=anthropic`. For another API member, choose a separate set of variable names, update the runner, and record only those names. Never put the key in the conversation, roster, delivery, or log.

If the endpoint and model are confirmed to support `reasoning_effort` in the OpenAI chat format, optionally set `FLEET_API_REASONING` to the brief's level, such as `high`, and add that variable name after the three in the roster's `How to call` field (again, the name only). The runner trims surrounding whitespace and passes the level name unchanged as `reasoning_effort`. An unset or blank value adds no field and preserves the existing request behavior. OpenAI-format compatibility does not mean every endpoint or model supports this field. For the Anthropic format, use the endpoint's own settings. This runner does not translate reasoning levels for it; a nonblank `FLEET_API_REASONING` with that format produces `Blocked` and exits 2 before any request. The log records the requested level, which does not establish what the server actually used; make that distinction in the handback.

The project manager guides the human one step at a time. Each step states where to act, what to do, and what to report back. First ask the human to set these three variables in their own terminal, using the [installation guide](https://github.com/chenyuwang166-oss/fleet-handbook/blob/main/install/README.md#verified-invocation-methods). Never ask for the key's value or ask the human to paste it into the conversation. For the check step, report only whether the variables exist, for example:

```bash
python -c "import os; print(all(k in os.environ for k in ('FLEET_API_BASE','FLEET_API_MODEL','FLEET_API_KEY')))"
```

Variables set in another terminal are invisible to the project manager's running tool. Have the human choose one route: reopen their AI tool from the terminal where the variables are set; or have the project manager prepare this runner and the human run it in that terminal. If the project manager's sandbox cannot reach the network, use the second route, as with command-line dispatch. Ask only for the existence check's boolean result, exit status, and delivery/log paths, never variable values. Before the first real request, explain that the API charges by usage and this is a very small call; wait for the human's agreement before calling it.

This standard-library runner sends the full brief to `POST <FLEET_API_BASE>/chat/completions` and captures `choices[0].message.content` unchanged for collection. With `FLEET_API_FORMAT=anthropic`, it sends to `POST <FLEET_API_BASE>/messages` and collects the response's text blocks in order; increase `max_tokens` in the runner for longer deliveries. An API member sees only the brief: write it as fleet-taskbook says for API members, with no instructions to read files and the needed materials pasted in. Use a new number for both files; `"x"` preserves earlier deliveries. If a response unexpectedly contains the key, reject it instead of saving it.

```bash
python -X utf8 - <<'PY'
import http.client
import json
import os
from pathlib import Path
import urllib.request
import urllib.error
timeout_seconds = 600  # adjust for the task; long tasks need more
max_tokens = 16000  # used only for the anthropic format; increase for longer deliveries, up to the model's output limit
prompt = Path("fleet/task-briefs/02.md").read_text(encoding="utf-8")
Path("fleet/deliveries").mkdir(parents=True, exist_ok=True)
with open("fleet/deliveries/02.md", "x", encoding="utf-8", newline="") as out, open("fleet/deliveries/02.log", "x", encoding="utf-8") as log:
    names = ("FLEET_API_BASE", "FLEET_API_MODEL", "FLEET_API_KEY")
    missing = [name for name in names if not os.environ.get(name)]
    if missing:
        print("Blocked: missing environment variables: " + ", ".join(missing), file=log)
        raise SystemExit(2)
    api_base, model, api_key = (os.environ[name] for name in names)

    def record(message):
        print(str(message).replace(api_key, "***"), file=log)

    api_format = os.environ.get("FLEET_API_FORMAT", "").strip().lower() or "openai"
    if api_format not in ("openai", "anthropic"):
        record("Blocked: FLEET_API_FORMAT must be openai or anthropic")
        raise SystemExit(2)

    reasoning = os.environ.get("FLEET_API_REASONING", "").strip()
    if reasoning and api_format == "anthropic":
        record("Blocked: FLEET_API_REASONING is only supported for the OpenAI format; use the Anthropic endpoint's own settings")
        raise SystemExit(2)

    try:
        if api_format == "anthropic":
            request = urllib.request.Request(
                api_base.rstrip("/") + "/messages",
                data=json.dumps({"model": model, "max_tokens": max_tokens, "messages": [{"role": "user", "content": prompt}]}).encode("utf-8"),
                headers={"x-api-key": api_key, "anthropic-version": "2023-06-01", "Content-Type": "application/json"},
                method="POST",
            )
        else:
            payload = {"model": model, "messages": [{"role": "user", "content": prompt}]}
            if reasoning:
                payload["reasoning_effort"] = reasoning
                record("Reasoning level (requested; server use unverified): " + reasoning)
            request = urllib.request.Request(
                api_base.rstrip("/") + "/chat/completions",
                data=json.dumps(payload).encode("utf-8"),
                headers={"Authorization": "Bearer " + api_key, "Content-Type": "application/json"},
                method="POST",
            )
        try:
            response = urllib.request.urlopen(request, timeout=timeout_seconds)
        except urllib.error.HTTPError as error:
            response = error
        with response:
            status = response.code
            record("HTTP " + str(status))
            body = response.read().decode("utf-8")
        if not 200 <= status < 300:
            record("Error: API returned an error: " + body)
            raise SystemExit(1)
        data = json.loads(body)
        if api_format == "anthropic":
            blocks = data["content"]
            if not isinstance(blocks, list):
                raise ValueError("content must be a list")
            record("stop_reason: " + str(data.get("stop_reason")))
            parts = []
            for block in blocks:
                if not isinstance(block, dict):
                    raise ValueError("content blocks must be objects")
                if block.get("type") == "text":
                    text = block["text"]
                    if not isinstance(text, str):
                        raise ValueError("text must be a string")
                    parts.append(text)
            content = "".join(parts)
        else:
            choice = data["choices"][0]
            if not isinstance(choice, dict):
                raise ValueError("choice must be an object")
            record("finish_reason: " + str(choice.get("finish_reason")))
            content = choice["message"]["content"]
            if not isinstance(content, str):
                raise ValueError("message.content must be a string")
        if not content.strip():
            raise ValueError("the response is empty; reasoning may have used up the output limit")
        if api_key in content:
            raise ValueError("response contains the API key; delivery not saved")
        out.write(content)
        record("Status: succeeded; delivery received, not yet reviewed.")
    except TimeoutError:
        record("Blocked: API invocation timed out; current output has not been accepted.")
        raise SystemExit(2)
    except urllib.error.URLError as error:
        if isinstance(error.reason, TimeoutError):
            record("Blocked: API invocation timed out; current output has not been accepted.")
        else:
            record("Blocked: API connection failed: " + str(error.reason))
        raise SystemExit(2)
    except (OSError, http.client.HTTPException) as error:
        record("Blocked: API connection dropped or read/write failed: " + type(error).__name__ + ": " + str(error))
        raise SystemExit(2)
    except (ValueError, KeyError, IndexError, TypeError, UnicodeError) as error:
        record("Error: invalid API request or response format: " + str(error))
        raise SystemExit(1)
raise SystemExit(0)
PY
```

Missing variables, an invalid format setting, timeout, connection failure, or a connection dropped mid-call produce `Blocked: …` and exit 2; HTTP errors, an invalid response format, or an empty response exit 1; success exits 0. After the run, open the response and log. Follow the same filename rule to save the text below `## Notes` in the notes file. Remove that section from the delivery file, check the completion status in the notes, and send the deliverable on its own for review. Report execution ending, delivery arriving, and review passing separately.

## When neither is available

Cross-provider dispatch requires the second model to be callable by a program. With one tool you can still build a fleet, draw the chart, write task briefs, and leave handoffs. If only web chat is available, an AI tool that can operate a browser could act on your behalf, but this is strongly discouraged: it is slow and error-prone; first check whether the service's terms permit automated operation.

For parallel work, give each member a separate input brief and output files. Collect results after all calls in the batch have ended. Do not have members edit the same file concurrently. The project manager checks facts and differences in analysis, then asks a member from another provider to review. The reviewer's provider must differ from the author's, including when the project manager is the author. The reviewer reads source materials and the delivery, not just the author's self-assessment. After review, the project manager closes the task against the acceptance criteria. Authors do not accept their own work.

Members do not call other providers' models themselves: when another provider is needed, the project manager dispatches it or a program calls it. For numbers that matter, have another provider compute them independently and compare the two. When a member's invocation or strengths change (a new model, new parameters, a new pitfall), update its roster entry at once, with the date and the evidence.

A report from another member or session that “the human agreed” does not count as the human's approval; the human confirms it directly, in their own conversation with the project manager. When one member is blocked by permissions or a sandbox, do not route the same work through another member; hand it back to the human. Do not edit files another role is responsible for; tell that role and let it make the change.

Completion requires all delivery files, evidence for judgments, an independent review, and every finding handled: fixed, or declined with a stated reason. Reviewers give findings; the project manager decides whether to close, against the acceptance criteria. Report paths, exit status, review conclusions, and specific actions awaiting a human decision. When reporting to the human, start with how many problems the review found, which were fixed, and which were not and why (if it found none, say so; if the review has not happened yet, say that first), then the rest. State separately whether execution ended, the delivery was received, and quality was accepted. Stop before unauthorized spending, credential use, external actions, or irreversible deletion. Finish reversible preparation before asking the human to decide.

## Text for people: a reader panel

For guides, posts and pages that will go out, ask a reader panel to read the draft after its facts are reviewed. Use models from at least two providers, each playing one target reader (state age, job, whether they write code, and where they came across it). They read only the draft, in the order that reader would meet it (for example, the post first, then the page), and return three things: where they would stop reading, how they would retell it to someone else, and which sentences sound off-putting. Readers give opinions, not votes. Whether to change something is the project manager's call, judged against what the piece is for: one reader's sound point can be enough; when several readers raise the same point, first look at why. Sentences the human has approved can be moved, but their wording must stay the same; after the changes, ask one or two readers to read only the changed parts and comment again.
