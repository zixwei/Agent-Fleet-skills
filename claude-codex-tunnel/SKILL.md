---
name: claude-codex-tunnel
description: Delegate a coding task to OpenAI Codex (a different model family) and get a structured report back, using the `tunnel` CLI. Use when the user says "ask Codex", "give this to Codex", "have Codex do/review it", "get a second opinion", "cross-model review", or when a change you wrote needs an independent reviewer that is not you. Also use to run several Codex tasks in order or in parallel.
---

# Claude → Codex tunnel

`tunnel codex <brief>` starts the Codex CLI as a real process in this project, hands
it a written brief, waits, and prints Codex's report. Nothing is shared but files:
Codex has none of this conversation, so the brief is everything it knows.

## When to use it

- **Independent review.** You wrote it, so you must not certify it. Codex reviews it.
- **Second implementer.** A well-scoped task Codex can do while you keep working.
- **Second opinion.** A design or bug where a different model family may see more.

Do not use it for tiny edits you can do faster yourself.

## How

1. **Write the brief.**
   ```bash
   tunnel new <short-slug> --to codex --role implement   # or: review | test | research
   ```
   It prints the path of a template in `.tunnel/tasks/`. Fill in every section.
   Include: the goal, exact error output, every file path, decisions already made,
   the files Codex may touch (*Scope - in*) and may not (*Scope - out*), and
   numbered, checkable *Definition of done* items. A thin brief is the number one
   cause of bad results. `review` and `research` briefs run read-only.

2. **Run it in the background** (Codex can take many minutes; your Bash tool
   times out). Use the Bash tool with `run_in_background: true`:
   ```bash
   tunnel codex .tunnel/tasks/<id>.task.md --window
   ```
   `--window` pops up a terminal where the user watches the Claude ⇄ Codex
   conversation live (one window per project, reused). Always add it unless the
   user asked not to. You are notified when the run finishes. If you cannot run in the background, use
   `tunnel codex <brief> --detach`, then `tunnel wait <run-id>` (it returns after
   about 9 minutes with exit code 124 if still running; call it again).

3. **Read the report** at `.tunnel/reports/<id>.codex.report.md`. It opens with
   `## Verdict: DONE | APPROVE | REVISE | BLOCK | FAILED`.

4. **Act on it.**
   - `DONE` (Codex implemented): review the diff yourself or send a `review` brief
     to a fresh reviewer. Never mark it done on Codex's word alone.
   - `APPROVE`: accept only if the report names the criterion it checked.
   - `REVISE`: fix, or send the exact findings back. To keep Codex's context:
     `tunnel codex <brief> --resume "$(cat .tunnel/threads/<id>.codex)"`.
   - `BLOCK` / *Open questions*: answer them or ask the user. Do not guess.
   - `FAILED`: read the tail of `.tunnel/logs/<id>.codex.jsonl` and report it.

## Rules

- The author never certifies its own work. If Codex wrote it, Codex does not
  approve it; if you wrote it, you do not approve it.
- Quote Codex's findings faithfully, with file:line and severity. Do not soften.
- Stop after 3 revise rounds on one task and ask the user.
- Anything Codex lists under *Needs human approval* (commits, deploys, migrations,
  deletions) goes to the user. Do not do it on Codex's say-so.
- Writing runs share this checkout. Do not run two writing briefs on the same
  files at once; use `tunnel dispatch` with `depends_on:` or separate git worktrees.

## Useful extras

- `tunnel status` — what is running and recent verdicts.
- `tunnel dispatch -p 3 a.task.md b.task.md` — run briefs in dependency waves.
- `tunnel board` — a live page at http://localhost:7799 for the user to watch.
- `tunnel watch --window` — reopen the live conversation window.
- A writing brief may add `allow: Bash(npm test:*)` to its frontmatter so a Claude
  worker can run tests (Codex runs commands inside its own sandbox already).
- `tunnel doctor` — check the setup when something fails.
