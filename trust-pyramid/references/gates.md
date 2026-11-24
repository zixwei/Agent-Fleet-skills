# Gates - making the checks unskippable

Prose can be skipped. Gates run automatically at fixed moments. Layer them: early gates are fast,
late gates are impossible to bypass. Every gate runs the same `scripts/check.sh`, so a failure
looks identical everywhere and "passes locally, fails in CI" does not happen.

| # | When | Mechanism | Purpose | Bypassable? |
|---|---|---|---|---|
| 1 | while coding | agent runs typecheck/lint | fastest feedback | yes |
| 2 | agent tries to finish its turn | Stop hook (Claude Code, Codex) | no "done" until checks pass | hard; config is local |
| 3 | `git commit` | `.githooks/pre-commit` | also covers humans and other tools | `--no-verify` |
| 4 | pull request | CI: check + smoke + AI review + review gate, **using the base branch's gate files** | runs on the server | **no**, with branch protection (+ code owners for teams); advisory where the plan has no branch protection |
| 5 | merge | human approval | looks at evidence, not diffs | final say |

## The gates must not trust the code they check

A PR is written by the agent being checked, and the gate definitions live in the same repo. In a
field test, an agent's PR could pass naive CI by turning the rule off in the lint config, deleting
the check from `scripts/check.sh`, or adding the source folder to the linter's ignore list; local
hooks fall to the same tricks. Three defenses, all installed by this skill:

1. **Trusted CI** - `scripts/trusted-check.sh <base>` restores every path in
   `quality/protected-paths.txt` (read from the base commit) before running the checks, and CI runs
   the base commit's copy of that script and of `review-gate.mjs`. Loosening a rule never applies
   to the PR that loosens it; stricter rules apply after they merge. `package.json` cannot be
   protected as a whole (it carries dependency changes), so trusted-check restores only its
   `"scripts"` from the base: a PR that changes `"lint": "tsc --noEmit"` to `"lint": "true"` is
   still checked with the old script. It is the CI entry point: run locally, it rewrites the
   protected paths in the working tree to the base versions, so run it only in CI or in a
   throwaway clone.
2. **No new suppressions** - `scripts/no-suppressions.sh` fails when a change adds
   `eslint-disable`, `@ts-ignore`, `# noqa`, `# type: ignore` and similar, and when a test file gains
   a skipped or focused test (`it.skip`, `test.only`, `describe.skip`, `xit`, `{ skip: true }`,
   `@pytest.mark.skip`, `pytest.skip()`, `@unittest.skip`, Go `t.Skip()`). Exceptions go into
   `quality/allowed-suppressions.txt`, which is protected. A deleted test is not detected.
3. **Humans own the gate files** - `.github/CODEOWNERS` lists the protected paths; enable "Require
   review from Code Owners". Claude Code's `permissions.deny` in `.claude/settings.local.json` blocks
   the agent's edit tools on those paths (friction only: shell commands can still change files, which
   is why 1 and 2 exist). It is per machine on purpose: rule-writing sessions run in a separate git
   worktree where the file is absent (see "Changing gate files" below).

Local gates (Stop hook, pre-commit) are for fast feedback and can be defeated by an agent that
edits them; only trusted CI with branch protection is unskippable. Say this plainly to the user.

## Windows: line endings and the executable bit

**Executable bit.** Git for Windows runs with `core.fileMode=false`, so `chmod +x` never reaches the
index and the scripts are committed as `100644`. Linux CI and macOS/Linux clones then fail with
`Permission denied` wherever a script is run directly, and git skips a `.githooks/pre-commit`
without the bit. The templates call every script through `bash <script>` so the mode matters less,
but git itself runs hooks directly. After copying the templates, on every OS:

```bash
git add scripts .githooks
git update-index --chmod=+x scripts/*.sh .githooks/*
git ls-files -s scripts .githooks   # every *.sh and hook must show 100755
```

**Line endings.**

Git for Windows defaults to `core.autocrlf=true`, which checks text files out with CRLF line
endings. bash cannot run a CRLF script, and hooks copied from a CRLF checkout carry CRLF too.
Install `templates/gitattributes` as (or append it to) `.gitattributes` **before** the first commit
of the gate files, so every clone gets LF for `scripts/`, `.githooks/`, `lint/`, `quality/`,
`verify/`, `.github/`, `.claude/` and `.codex/`. If gate files were already committed from a CRLF
working tree, run `git add --renormalize .` once and commit the result.

## scripts/check.sh

Copy `templates/scripts/check.sh` and edit the `CHECKS` table (each entry: space-separated path
prefixes, a label, a command). Behavior:

- Runs only for packages with changes (`--staged` for pre-commit, `--all` for CI).
- Prints only failures. It recognizes `node --test` (spec and TAP), pytest (`-rf --tb=short`) and
  jest/vitest/go output; long lines are cut (assertion dumps of whole files hide the real error).
  For other runners, pick a reporter that prints a failure summary and extend `summarize()`.
- Exit code is the only contract: 0 pass, 1 a check failed, 3 setup needed (below).
- **Dependencies (`SETUP`).** List what the checks need installed, one line per package:
  `<path that exists once installed>|<install command>|<path prefixes>`, for example
  `"web/node_modules/.package-lock.json|npm --prefix web ci|web/ shared/"` (npm writes that file
  last, so an install still running counts as missing). Use the prefixes of the checks that need the
  package; without prefixes the entry applies to every change. A fresh git worktree has none of it,
  and a missing linter or compiler reads like a failure in the change. When the change touches the
  prefixes of a missing entry (`--all`: always), `check.sh` runs no check, prints `setup needed`
  with the install commands, and exits 3; `scripts/check.sh --setup` installs what is missing. A
  docs-only commit is not held up by a missing package. If a check fails while any entry is missing
  (its prefixes were narrower than the check's), the result is also reported as setup needed (exit
  3) and not logged. Make sure CI's install step creates the same paths.
- **Suites not run here (`NOT_RUN`).** Every test script of the project (`package.json` `test*`
  scripts, pytest markers, emulator or database suites) is either a `CHECKS` entry or a `NOT_RUN`
  entry, `<label>|<reason and where it does run>`. `check.sh` prints them after the results
  (`• not run here: ...`, not in `--fast` runs), so a green list is not read as "every test ran".
  When a change touches code covered only by such a suite, run that suite before saying it works.
- `SETUP` and `NOT_RUN` sit in the `trust-pyramid:project-start local` section, which upgrades keep
  as the project wrote it.
- Every check has a time limit (120 s for `fast`, 900 s otherwise; `TRUST_CHECK_TIMEOUT` or a
  per-check `fast,timeout=<seconds>` changes it). A check that runs out is stopped with its child
  processes and reported as `✗ <label> (timed out after Ns)` with a hint: usually a socket, server
  or timer left open. Also give the test runner its own limit (`node --test --test-timeout=<ms>`,
  `pytest-timeout`, `go test -timeout`), and set `timeout-minutes` on CI jobs (the templates do).
- Prefer calling tools directly (`node_modules/.bin/tsc -p web --noEmit`, `node lint/oxlint.mjs ...`,
  the test runner with explicit files) over `npm run <script>`: what a direct command does is defined
  in `scripts/check.sh`, which is protected. When a check does go through an npm script,
  trusted-check uses the base branch's `"scripts"` for every `package.json` the base has.

## Pre-commit

`templates/githooks/pre-commit` → `.githooks/pre-commit`, then `git config core.hooksPath .githooks`
(`scripts/install-agent-hooks.sh` asks and does it). On every commit it also runs
`scripts/hook-status.sh --quiet` and prints a reminder while the agent Stop hook is not enabled; the
reminder never blocks the commit.
Document the one-time `git config` in the README. In a project with a root `package.json`, add
`"prepare": "git config core.hooksPath .githooks || exit 0"` to its `scripts` (merge with an existing
`prepare` using `&&`), so `npm install` enables pre-commit in every clone, including teammates'.
`|| exit 0` works in both sh and Windows cmd and keeps installs outside a git checkout working.

Reminders that need no hook at all: the root `AGENTS.md` tells the agent to run
`scripts/hook-status.sh` at the start of each session and, if it fails, tell the user how to enable
the gates. This is prose (an agent can skip it), but it reaches a user who never ran the installer,
which a reminder inside a hook cannot.

## Stop hook (one script, both agents)

`templates/scripts/stop-gate.sh` → `scripts/stop-gate.sh` (one script for both agents). Behavior:

- Finds the repository from its own location, not from the current directory (the agent may have
  changed directory, or the host may start hooks elsewhere). If it cannot find it, it prints
  `stop-gate: cannot find the repository` as a visible `systemMessage` and lets the turn end: never
  silent, and never a block the agent cannot clear (exit 2 there would loop until the host's cap).
- Also checks the linked git worktrees this session works in, with each worktree's own
  `scripts/check.sh`: the host starts the hook from the session's checkout, but agents often edit
  in a worktree. "Works in" means the hook input's `cwd` is inside it, or the session's transcript
  (`transcript_path`) mentions its path. Other sessions' worktrees are not checked, so their
  failures and slow tests never block this session. Failures are labeled with the worktree path.
  Paths are compared after normalizing (`C:\\a` in JSON, `C:/a` from git, `/c/a` in Git Bash,
  `/cygdrive/c/a`, any letter case), and only as a whole path: a mention of `../app-feature/x`
  does not select the worktree `../app`.
  `STOP_GATE_WORKTREES=all` (or `1`) checks every worktree with uncommitted changes, `none` (or `0`)
  only the session's checkout; another value checks every worktree and says so. The per-machine deny list does not apply in worktrees, by design (gate changes
  are made there and reach the default branch through a reviewed pull request).
  Register it as `bash "$CLAUDE_PROJECT_DIR/scripts/stop-gate.sh"` (Claude Code) so neither the
  executable bit nor the shebang matters (Windows). Codex has no project-directory variable; its
  command finds the repository from the session directory (subfolders work) and, outside any
  repository, prints the same kind of visible `systemMessage` instead of failing with exit 127.
- Runs `scripts/check.sh`. Pass: prints `{}` to stdout and exits 0 (Codex requires JSON on stdout
  when a Stop hook exits 0; Claude Code accepts it).
- Fail: prints `{"decision": "block", "reason": "<failures>"}` to stdout and exits 0. Both hosts
  document this for Stop: the agent keeps working with the reason (Codex sends it as the next
  prompt). It does not exit 2 with the failures on stderr: Claude Code reads a Stop hook that exits 2
  with `No such file or directory` in stderr as a missing script and lets the turn end, and a failing
  check can print exactly that. Keep the blocking path free of stray stderr.
- Missing dependencies (`check.sh` exit 3, usual in a fresh worktree): blocks with "setup needed,
  run `scripts/check.sh --setup`", which does not use up a fix attempt. After
  `STOP_GATE_MAX_ATTEMPTS` such stops in a row it lets the turn end with a visible message.
- Runs only checks marked `|fast` in `scripts/check.sh` (typecheck, lint, quick tests), so ending a
  turn costs seconds. Pre-commit and CI run everything.
- Checks only **uncommitted** changes (`git diff HEAD` plus untracked files). Work the agent already
  committed but has not pushed is not checked by the Stop hook; pre-commit checked it at commit time
  and CI checks it on the PR. The hook is fast feedback, not a guarantee.
- Shared or local: committed in `.claude/settings.json`, the hook runs for everyone who uses Claude
  Code on the project; in `.claude/settings.local.json` it runs only on this machine. Ask the user in
  Phase 5. Codex reads `.codex/hooks.json` (committed) and asks each user to trust it.
- Only runs started by the host count: the hook input on stdin carries `hook_event_name` and
  `session_id`. Attempts are counted per session, and only those runs update
  `.quality-state/stop-gate-last-run` (the proof `hook-status.sh` shows). Running the script by hand
  (`echo '{}' | bash scripts/stop-gate.sh`) reports the same result and counts nothing.
- After `STOP_GATE_MAX_ATTEMPTS` failed attempts (default 2) it blocks once more with "stop and
  explain the failure to the user", then lets the turn end with a visible `systemMessage`. The
  failure message also tells the agent to hand over at once when the fix needs a protected file.
  In a benchmark, a 3-attempt cap plus protected-file failures made one task cost 4x the baseline.

Registration (staged by the agent, installed by the user with `scripts/install-agent-hooks.sh`,
because each agent protects its own config folder; in testing, Claude could not write
`.claude/settings.json` and Codex could not write `.codex/`):
- Claude Code: `templates/claude/settings.json` → `quality/install/claude-settings.json` → `.claude/settings.json`
  (the hook), and `templates/claude/settings.local.json` → `quality/install/claude-settings.local.json` →
  `.claude/settings.local.json` (the deny list).
- Codex: `templates/codex/hooks.json` → `quality/install/codex-hooks.json` → `.codex/hooks.json`.
  Codex only loads project hooks from trusted projects and asks the user to review and trust new or
  changed hooks; tell the user to approve it once.

A hook that is not registered never runs, and nothing tells the user. So:
- `scripts/install-agent-hooks.sh` (the user runs it) shows each change and asks, then merges the
  staged config into the existing file: hook entries are added unless that command (or any
  `scripts/stop-gate.sh` entry) is already there, permission lists are unioned, nothing is removed,
  and the previous file is kept as `<file>.bak`. `--yes` applies without asking.
- `scripts/hook-status.sh` reports, per clone: the Claude Code and Codex Stop hooks (for the hosts
  staged in `quality/install/`), `disableAllHooks` in user or project settings, pre-commit, and
  when the Stop hook last ran (`stop-gate.sh` writes `.quality-state/stop-gate-last-run`). Exit 1
  when something expected is missing. Setup does not pass Phase 5 until it is clean.

## CI

One workflow per layer, so CI never turns red for a layer that is not installed. Install only the
ones that apply:

| Template | Jobs (status checks) | Install when |
|---|---|---|
| `templates/github/workflows/quality.yml` | `check` - `scripts/check.sh --all` | always |
| `templates/github/workflows/quality-smoke.yml` | `smoke` - `node verify/app.mjs up && node verify/smoke.mjs`, uploads screenshots | `verify/` is installed (Layer 3) |
| `templates/github/workflows/quality-review.yml` | `ai-review` - the reviewer once per code diff (cached by diff hash), uploads `review.json`, posts findings as a PR comment; `review-gate` - `node scripts/review-gate.mjs` (see `layer-5-ai-reviewer.md`) | the reviewer is installed **and** the API key secret is set |

A lite setup without `verify/` or without an API key starts with `quality.yml` alone; add the
others later by copying them in, then add their status checks to branch protection. Edit the
dependency install step (`TODO(trust-pyramid)`) in each copied workflow.

### Bootstrap: the PR that introduces the gates

CI runs the base branch's gate files, and on the PR that adds them the base has none. The workflow
detects this (`git cat-file -e "$BASE_SHA:scripts/trusted-check.sh"`) and runs the PR's own copies
instead, labelled with a `Gate bootstrap` warning and a line in the job summary. Without that
check, `git show` stops the job with `fatal: path 'scripts/trusted-check.sh' exists on disk, but
not in '<base sha>'` (exit code 128), which reads like a broken setup. Tell the user:

- The gate PR is checked by its own (untrusted) gate files; that is expected once. Every PR opened
  after it merges is checked with the base branch's copies.
- To see the trusted path work before merging into the default branch, stack the branches: merge
  the gate PR into a throwaway base branch first, open a small probe PR against that branch, and
  confirm the `check` job uses the base copies (no `Gate bootstrap` warning). Then merge the gate PR
  into the default branch.

## Branch protection (the user must do this once)

Branch protection is what makes the checks mandatory. Check availability in Phase 0, not after
building: on GitHub Free, private repositories get neither branch protection nor rulesets
(`gh api repos/<owner>/<repo>/branches/main/protection` and `.../rulesets` return 403 "Upgrade to
GitHub Pro or make this repository public to enable this feature"). Public repositories and paid
plans have both.

**Team (two or more people who can approve).** Settings → Branches (or Rules → Rulesets) → protect
`main`: require a pull request, require the status checks you installed (`check`, and `smoke`,
`review-gate` if installed), require 1 approval, require review from Code Owners, and do not allow
bypassing. Or with the GitHub CLI (`gh api -X PUT repos/<owner>/<repo>/branches/main/protection ...`).

**One maintainer.** GitHub does not let an author approve their own PR, so required approvals or
code-owner review would block every merge. Require a pull request and the status checks, with no
required approvals and no code-owner review. CODEOWNERS still marks gate files in the PR view, and
trusted CI still checks every PR with the base branch's gate files.

**No branch protection available (private repository on GitHub Free).** The PR checks still run but
are advisory: nothing stops a merge while they are red. Tell the user plainly, and set up the
fallback:
- check the PR's checks before every merge (`gh pr checks <n> --watch`) and never merge on red;
- make the deploy workflow run the same checks (`scripts/check.sh --all` and the tests) before it
  deploys, so a red change never reaches users - that is the hard stop;
- revisit when the repository becomes public or the plan changes.

Explain to the user in plain language which of these applies and what it means.

## Changing gate files (new rules) without switching the deny list off

The deny list keeps the agent away from `lint/`, `scripts/` and the other gate files during feature
work, and `references/add-rule.md` asks the agent to write rules there. Both hold with one model:

1. Feature work happens in the main checkout, where `.claude/settings.local.json` denies edits to
   gate files.
2. A rule change happens in a separate worktree:
   `git worktree add ../<repo>-rule-<name> -b rule/<name>`, then `bash scripts/check.sh --setup`
  there, then start the agent there. The deny list
   is per machine and not committed, so it is absent in the new worktree (do not list it in a
   `.worktreeinclude` file). The agent writes the rule, fixtures and test, runs `scripts/check.sh`,
   commits and opens a PR.
3. A human reviews and merges that PR (CODEOWNERS marks the gate paths; trusted CI checks it with
   the base branch's rules). The new rule applies to every PR after the merge.

Nothing is switched off in the main checkout, and the gate change gets a human review.

## Gate log: what the gates actually caught

With `TRUST_GATE` set, `scripts/check.sh` records every failing check through
`scripts/gate-log.sh`: the Stop hook sets `stop-hook` (only when the host started it), pre-commit `pre-commit`, and the CI `check`
job `ci`; trusted-check also records new suppression comments. Each block becomes one line in
`.quality-state/gate-log.jsonl` (date, gate, check label, rule IDs found in the output, such as
`project(no-raw-ui-text)` or `TP001`), and in CI a line in the job summary. Manual runs are not
logged, and `TRUST_GATE_LOG=0` turns logging off for probes that simulate a gate (proving a check
fires), so the report counts real interceptions only. The local log is per machine and gitignored; CI's record is the job summary. Checks that
fail in a linked worktree are logged in the main checkout's log, with a `worktree` field, so the
report includes them and they survive `git worktree remove`.

`node scripts/gate-report.mjs [--rules <id,id,...>]` prints blocks per week and gate, blocks per
rule, rules that fired in more than one week, (with `--rules`) rules that never fired, and the rows
of `quality/rule-candidates.md` at `Seen` 2 or more, which wait for the agent to add them as rules.
This answers the question users ask after setup: do the gates pay for themselves?
`scripts/hook-status.sh`, which the agent runs at the start of each session, lists the same rows.

## Optional rule pack: text that lands on GitHub

Some teams want every PR title, PR description and commit message in one language, and PR
descriptions free of AI tool or vendor markers ("Generated with ...", "Co-Authored-By: ...", the
robot emoji). Offer it in Phase 0; install only on request:

| Template | Install as |
|---|---|
| `templates/github-text/check-github-text.mjs` | `scripts/check-github-text.mjs` (edit `CONFIG` at the top) |
| `templates/github-text/commit-msg` | `.githooks/commit-msg` (`git update-index --chmod=+x`) |
| `templates/github-text/github-text.yml` | `.github/workflows/github-text.yml`; make `github-text` a required check |

- `CONFIG.language`: the language's name and the Unicode scripts its letters may use
  (`['Latin']` for English). The check works by writing system, so it catches Chinese, Japanese,
  Cyrillic and similar, not another Latin-script language. `null` switches it off.
- `CONFIG.markersIn` (default: PR descriptions only) and `CONFIG.markers` (the names and phrases
  to reject). Code spans and fenced blocks are skipped, so `CLAUDE.md` in backticks or a quoted UI
  string stays allowed.
- The workflow runs the base branch's copy (bootstrap fallback on the PR that adds it), passes PR
  text through the environment only, and re-runs when the title or description is edited.
- Before `gh pr create` or `gh pr edit`, the agent writes the description to a file and checks it
  locally (the root `AGENTS.md` says so):
  `PR_TITLE="..." PR_BODY="$(cat body.md)" node scripts/check-github-text.mjs --pr`. Otherwise a
  bad description is found only by CI, after the PR is open.

## PR template

`templates/github/pull_request_template.md`: plain-language summary, smoke result, screenshots,
reviewer findings with dispositions. The human approves from this page.
