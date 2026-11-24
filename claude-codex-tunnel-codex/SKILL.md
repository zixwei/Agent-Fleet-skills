---
name: claude-codex-tunnel
description: Delegate a coding task to Claude Code (a different model family) and get a structured report back, using the `tunnel` CLI. Use when the user says "ask Claude", "give this to Claude", "have Claude do/review it", "get a second opinion", "cross-model review", or when a change you wrote needs an independent reviewer that is not you.
---

# Codex → Claude tunnel

`tunnel claude <brief>` starts Claude Code (`claude -p`) as a real process in this
project, hands it a written brief, waits, and prints Claude's report. Claude has
none of this conversation, so the brief is everything it knows.

## Important: run it outside the sandbox

Claude Code needs the network and its own sign-in files, which the Codex sandbox
blocks. Run every `tunnel` command with escalated permissions (outside the
sandbox). If the user installed with `--trust`, `tunnel` is already allowed.

## How

1. **Write the brief.**
   ```bash
   tunnel new <short-slug> --to claude --role implement   # or: review | test | research
   ```
   It prints the path of a template in `.tunnel/tasks/`. Fill in every section:
   goal, exact error output, every file path, decisions already made, *Scope - in*,
   *Scope - out*, and numbered, checkable *Definition of done* items. `review` and
   `research` briefs run read-only.

2. **Run it.** Claude can take many minutes, so detach and wait:
   ```bash
   tunnel claude .tunnel/tasks/<id>.task.md --detach --window   # prints a run id
   tunnel wait <run-id>                                  # exit 124 = still running, call again
   ```
   Give the wait command a long timeout (10 minutes). `--window` pops up a terminal
   where the user watches the Codex ⇄ Claude conversation live. Always add it
   unless the user asked not to.

3. **Read the report** at `.tunnel/reports/<id>.claude.report.md`. It opens with
   `## Verdict: DONE | APPROVE | REVISE | BLOCK | FAILED`.

4. **Act on it.**
   - `DONE`: check the diff yourself or send a `review` brief. Never accept on
     Claude's word alone.
   - `APPROVE`: accept only if the report names the criterion it checked.
   - `REVISE`: fix, or resume Claude with the findings:
     `tunnel claude <brief> --resume "$(cat .tunnel/threads/<id>.claude)"`.
   - `BLOCK` / *Open questions*: answer them or ask the user.
   - `FAILED`: read the tail of `.tunnel/logs/<id>.claude.jsonl` and report it.

## Rules

- The author never certifies its own work.
- Quote Claude's findings faithfully, with file:line and severity.
- Stop after 3 revise rounds on one task and ask the user.
- Anything under *Needs human approval* goes to the user, never done on Claude's say-so.
- By default a writing Claude run may read, edit and run read-only git commands.
  To let it run tests, add a line to the brief's frontmatter, e.g.
  `allow: Bash(npm test:*),Bash(python3 -m pytest:*)`.
