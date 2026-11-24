---
name: trust-pyramid
description: Set up (or extend) a project-specific quality system where coding agents prove their own work - architecture constraints, custom lint rules with fix-it error messages, runtime verification, an evidence-based AI reviewer, and gates (agent Stop hook, pre-commit, CI, branch protection) that cannot be skipped. Use when starting a new project, when adding agent guardrails to an existing repo, when upgrading an existing Trust Pyramid installation, or when the user wants to stop reviewing AI-written code line by line. Works in Claude Code and Codex.
license: MIT
---

# Trust Pyramid

Goal: the human stops reading diffs. Agents write code that is correct by default, are stopped by
deterministic checks when it is not, prove behavior in the running app, and hand the human
evidence (screenshots, smoke results, red-to-green tests) plus a plain-language summary.

The method follows Lauren Tan's talk "All Code Is Slop (Except Mine)" (AI Engineer Code Summit 2026)
and adds an AI reviewer layer for the early phase when there are few rules and the human cannot spot problems.

## Core rules (apply to everything you build with this skill)

1. **Prefer the highest layer that can solve the problem.** In order:
   architecture/API design → static analysis → verification → prose → AI reviewer → human.
   Prose (AGENTS.md, skills) is what agents may skip; checks that run automatically are not.
2. **Code is memory.** Agents copy what is in the repo. Never leave workarounds, anti-patterns or
   dead code behind as examples.
3. **Every correction becomes a rule or a test.** Eliminate by design if possible, otherwise lint,
   otherwise test, and only then prose.
4. **Error messages are prompts.** Every custom rule says what is wrong, why, how to fix it, and the
   sanctioned escape hatch for legitimate exceptions.
5. **No escape hatches.** No disable comments, no `--no-verify`, no weakened assertions, no excuse
   comments ("too hard to change, patch for now").
6. **Ratchet.** Lock in what is already clean; rules with many existing violations start on new code
   only and are tracked until the backlog is cleared.
7. **The checker is never the checked.** Anything that must not be skipped runs outside the agent's
   control, and must not trust files the agent can edit: CI runs the base branch's gate files
   (`scripts/trusted-check.sh`), new suppression comments fail, and humans own the gate files.
8. **Fix the inner loop before automating the outer loop.**

## Profiles: lite (default) and full

Benchmarks with frontier models (see the repo's `docs/BENCHMARK.md`) showed that a strong model
already gets most single tasks right, including races and cross-feature invariants. Per-task AI
review and repeated Stop-hook loops then add cost (~4x per task) without catching more. What paid off:
project-specific rules a model cannot know, regression tests, and gates an agent cannot switch off.
So the default is lite.

| Part | lite (default) | full |
|---|---|---|
| Structure + single-entry APIs (Phase 2) | yes | yes |
| Project lint rules + boundary rules + tests (Phase 3) | yes | yes, plus property tests |
| Runtime verification (Phase 4) | smoke flow in CI; verify CLI available, not required per task | verify CLI + smoke on every user-visible change |
| Stop hook | fast checks only (`check.sh --fast`), 2 attempts | same |
| Pre-commit + trusted CI + CODEOWNERS (Phase 5) | yes | yes |
| AI reviewer (Phase 6) | once per PR in CI, enforced by `review-gate` | also locally after each task |
| Formal methods / UI fuzzing | no | when the plan justifies it |

Recommend **full** when: the coding agent uses a smaller or cheaper model, many agents or people
contribute, or the code handles money, permissions or personal data at scale. Ask in Phase 0 and
record the choice in `docs/QUALITY.md`; the user can switch later.

## Workflow

Follow these phases in order. Stop after Phase 1 and wait for the user's approval.

### Phase 0 - Intake

Collect (ask only for what you cannot detect from the repo):
product in one sentence, core user-facing features, stack, package manager and runtime versions,
UI languages, deployment target, areas involving money/permissions/privacy, external APIs
(AI models, payments), the user's coding level, new vs existing project.
Detect the host agent: Claude Code (`.claude/`, `CLAUDE.md`) or Codex (`.codex/`, `AGENTS.md`).
If unsure, install gates for both. Propose a profile (lite unless the full criteria apply).
Check what the hosting plan allows **before** building, and tell the user the result: branch
protection and rulesets are not available for private repositories on GitHub Free
(`gh repo view --json visibility`, then `gh api repos/<owner>/<repo>/branches/<default>/protection`;
a 403 "Upgrade to GitHub Pro or make this repository public" means not available). Ask whether the
project has one maintainer or a team. Both answers change the merge gate (`references/gates.md`,
"Branch protection").

### Phase 1 - Plan (then stop for approval)

Write a plan covering: directory layout and boundaries, tool choices with version-compatibility
notes, the first batch of rules (source, detection, draft error message), the verification
environment (including a fake model server if the app calls AI APIs), the reviewer design, the
gates, and the phases below. Read `references/stack-recipes.md` for the user's stack first.

### Phase 2 - Layer 1: structure and APIs

Read `references/layer-1-structure.md`. Create the layout and the single-entry APIs (AI client,
config/secrets, i18n, logging, shared UI primitives) so the wrong thing is hard to write.

### Phase 3 - Layer 2: static analysis

Read `references/layer-2-static.md`. Typecheck strict, boundary rules, a project lint plugin
(start from `templates/lint/`), each rule with violating + compliant fixtures and a test,
unit tests for pure logic, property tests for money/quota/permission/time logic, database
security-rule tests.

Before enabling each new rule, run it on the whole repository and record the count of existing
violations in `docs/QUALITY.md`. Zero: enable it everywhere. Otherwise fix them now if there are
few, or enable it for new code only and record the baseline (count and files) in `quality/` so the
backlog is tracked until it reaches zero (core rule 6, ratchet).

### Phase 4 - Layer 3: verification

Read `references/layer-3-verification.md`. One-command local stack with isolated ports and seeded
data, the verify CLI (`templates/verify/app.mjs`), a feature map, a smoke flow, and a fake
OpenAI-compatible server (`templates/verify/fake-openai.mjs`) when the app calls models.

### Phase 5 - Gates

Read `references/gates.md`. Install `scripts/check.sh`, the pre-commit hook, the Stop hook for
each host agent, CI, branch protection instructions, and the PR template. Install
`templates/gitattributes` first, and after copying the scripts run
`git update-index --chmod=+x scripts/*.sh .githooks/*` (on Windows `chmod +x` does not reach the
index, and Linux CI then fails with `Permission denied`).

In `scripts/check.sh`, list every test script the project has: each is a `CHECKS` entry or a
`NOT_RUN` entry with the reason (needs an emulator, a database...) and where it runs instead. Fill
`SETUP` with the install command of every package the checks need and the path prefixes of the
checks that need it, then prove it in a fresh worktree: `check.sh --all` says `setup needed`,
`check.sh --setup` installs, `check.sh --all` runs the checks. (Without changes, a plain `check.sh`
in a fresh worktree has nothing under those prefixes, so it does not ask for setup.)

Agents usually cannot write their own host config (Claude Code protects `.claude/settings*.json`,
Codex protects `.codex/`), and neither host runs a hook that is not registered in its config: the
Stop hook is OFF until the user enables it. Stage the hook configs in `quality/install/`
(`claude-settings.json`, `claude-settings.local.json`, `codex-hooks.json`; stage Codex only if the
user uses Codex), install `scripts/install-agent-hooks.sh` and `scripts/hook-status.sh`, and ask the
user to run `bash scripts/install-agent-hooks.sh` in a terminal. It shows each change, asks, merges
into existing config files (nothing is overwritten), and enables pre-commit. Then run
`bash scripts/hook-status.sh` yourself: **do not continue past Phase 5 until it reports every gate
enabled**, unless the user explicitly declines (record that in `docs/QUALITY.md`). In a project
with a root `package.json`, add the `prepare` script from `references/gates.md` so `npm install`
enables pre-commit in every clone. Tell the user to
start a new agent session afterwards: hooks are read when a session starts. Stage the `permissions.deny` list for protected paths **last**, after every
other edit: once it is in place the agent can no longer edit gate files (by design), so earlier
installation locks it out.

The deny list and the add-rule workflow need each other, so explain this model to the user: the
deny list lives in `.claude/settings.local.json` (per machine, not committed) and stays on for
normal feature work. Writing or changing a rule (`quality/ADD_RULE.md`, from `references/add-rule.md`) happens in a separate
git worktree on its own branch, where that file does not exist, and reaches the default branch only
through a PR that a human reviews (CODEOWNERS and trusted CI cover the gate paths).

Ask whether the Stop hook should be shared with everyone who uses Claude Code on the project
(committed in `.claude/settings.json`, the default) or enabled only on this machine
(`.claude/settings.local.json`). For local only, stage a single `claude-settings.local.json` that
holds both `hooks` and `permissions`, and no `claude-settings.json`. Tell the user what the hook
covers: uncommitted changes only (fast feedback, not a guarantee); pre-commit and CI cover commits.

### Phase 6 - Layer 5: AI reviewer (enforced)

Read `references/layer-5-ai-reviewer.md`. Install the reviewer prompt, the local reviewer agent,
the CI review job, and `scripts/review-gate.mjs` so every finding needs a machine-checked
disposition before merge.

### Phase 7 - Layer 4: prose

Write a short root `AGENTS.md` (Claude Code: `CLAUDE.md` containing `@AGENTS.md`) from
`templates/AGENTS.md`. Keep it under ~15 lines; rules live in code, not here.

### Phase 8 - Prove it and demo the loop

1. Run every check and the smoke flow; all must pass. Then commit to the setup branch (or ask the
   user to) and run the checks again on a fresh clone of that commit: gates that only pass on an
   uncommitted working tree are broken. Check `git ls-files -s scripts .githooks`: every script and
   hook must be `100755`, and `git ls-files --eol scripts .githooks` must show `i/lf`.
   Run `bash scripts/hook-status.sh`: every gate must be enabled, and after the user has ended one
   turn in a new session it must show when the Stop hook last ran (proof the host really runs it).
2. Prove no check is vacuous: introduce one violation per check (lint rule, boundary, test,
   smoke step), confirm it fails, then restore. Run these probes with `TRUST_GATE_LOG=0`, so they
   are not counted as interceptions in `scripts/gate-report.mjs`. Restore from a backup or with an exact edit,
   never with `git checkout` on files that hold uncommitted work.
3. Demo one small feature end to end: write a deliberately non-compliant version → gate blocks
   it → **run the reviewer blind on that pre-fix commit first** (no hints about the known problems)
   and record which of them it found on its own → fix from the error messages → verify in the
   running app with screenshots → run the reviewer again and confirm/reject findings with
   evidence → turn confirmed findings into rules → commit. Finish by removing the demo code unless
   it is a real feature the user wants to keep (code is memory: agents copy what is left behind).
   Record the real outputs, including the blind-review result, in `docs/QUALITY.md`.
4. Prove CI blocks a recurrence. A new rule only takes effect in CI after it merges (CI uses the
   base branch's rules), so the PR that adds it cannot show this. After the gates are on a base
   branch (the default branch, or a throwaway base as in `references/gates.md`, "Bootstrap"),
   open a throwaway probe PR based on it that reintroduces one mistake a rule catches, committed
   with `--no-verify` only for this probe (the local gates would stop it otherwise). Expect the
   CI `check` job to fail at the exact file:line, record the result, then close the PR and delete
   its branch without merging.
5. Record the installation for future upgrades: `node <skill>/tools/upgrade.mjs record --project .`
   (`<skill>` is this skill's directory) writes `quality/trust-pyramid.lock.json`. Commit it.
6. Report to the user in plain language: what was installed, how each layer was proven, what is
   still missing, and the one-time setup they must do themselves (secrets, branch protection).
   Tell them how the rules grow: when a mistake is corrected or repeats, the agent asks "may I add a
   rule for this?", and on a yes opens a small pull request that adds it (`quality/ADD_RULE.md`);
   their part is to say yes, then read its plain-language description and approve it.

Commit to a new branch. Never push or deploy unless the user asks.

## Templates

| Template | Install as |
|---|---|
| `templates/scripts/check.sh` | `scripts/check.sh` (edit `CHECKS`, `SETUP` and `NOT_RUN`; keep the `trust-pyramid:project-start/end` markers) |
| `templates/scripts/stop-gate.sh` | `scripts/stop-gate.sh` |
| `templates/scripts/install-agent-hooks.sh` | `scripts/install-agent-hooks.sh` (the user runs it; asks, then merges) |
| `templates/scripts/hook-status.sh` | `scripts/hook-status.sh` (is every gate on in this clone; pre-commit reminds until it is) |
| `templates/claude/settings.json` | staged as `quality/install/claude-settings.json`; the installer merges it into `.claude/settings.json`: the Stop hook |
| `templates/claude/settings.local.json` | `.claude/settings.local.json` (per machine): `permissions.deny` for gate files |
| `templates/codex/hooks.json` | `.codex/hooks.json` |
| `templates/githooks/pre-commit` | `.githooks/pre-commit` + `git config core.hooksPath .githooks` |
| `templates/lint/` | `lint/` (oxlint/ESLint plugin, fixtures, fixture test, `oxlint.mjs` cross-platform runner) |
| `templates/lint/python/` | `lint/` for Python projects (AST rule checker + fixtures) |
| `templates/verify/` | `verify/` (CLI, config, smoke, feature map, fake model server) |
| `templates/quality/` | `quality/` (reviewer prompt, review config, dispositions, ledgers) |
| `templates/scripts/review-gate.mjs` | `scripts/review-gate.mjs` |
| `templates/scripts/gate-log.sh`, `templates/scripts/gate-report.mjs` | `scripts/` (record what the gates block; weekly summary) |
| `templates/scripts/trusted-check.sh`, `templates/scripts/no-suppressions.sh` | `scripts/` (CI uses the base branch's copies) |
| `templates/quality/protected-paths.txt`, `templates/quality/allowed-suppressions.txt` | `quality/` (edit the list for the project) |
| `templates/quality/ADD_RULE.md`, `templates/quality/rule-candidates.md` | `quality/`: how the agent proposes a rule and, on a yes, adds it itself; and the ledger. Ledger reasons are never "protected" or "a maintainer must do it" |
| `templates/github/CODEOWNERS` | `.github/CODEOWNERS` (replace `@OWNER`) |
| `templates/claude/agents/quality-reviewer.md` | `.claude/agents/quality-reviewer.md` |
| `templates/github/` | `.github/` (PR template, and one workflow per installed layer: `templates/github/workflows/quality.yml` always, `templates/github/workflows/quality-smoke.yml` with `verify/`, `templates/github/workflows/quality-review.yml` with the reviewer and its API key) |
| `templates/github-text/` (optional) | `scripts/check-github-text.mjs`, `.githooks/commit-msg`, `.github/workflows/github-text.yml`: PR text and commit messages in the project's language, no AI tool markers in PR descriptions (`references/gates.md`) |
| `templates/AGENTS.md` | `AGENTS.md` (+ `CLAUDE.md` containing `@AGENTS.md`) |
| `templates/gitignore-additions.txt` | append to `.gitignore` |
| `templates/gitattributes` | append to `.gitattributes` (LF line endings for gate files on Windows) |

`install-manifest.json` is the machine-readable version of this table (path, and whether the file
is mechanism, tailored, content or append); `tools/upgrade.mjs` uses it.

Adapt every template to the stack; remove example rules that do not apply. Templates are
JavaScript/Node because the verification tooling (Playwright) is; for non-Node stacks keep
`verify/` as a small Node package and write lint rules in the stack's own linter.

## Daily use (after setup)

- **Upgrade to a new release of this skill** → follow `references/upgrade.md`: three-way merge that
  keeps the project's changes, in a worktree, through a pull request.
- **A mistake was corrected, or a rule candidate reached `Seen` 2** → the agent follows the project's
  `quality/ADD_RULE.md` (`references/add-rule.md`): after the task at hand it asks the user, and on a
  yes adds the rule itself.
- **Before a PR** → CI runs the reviewer; in the full profile also run it locally (`references/layer-5-ai-reviewer.md`).
- **Weekly sweep** → run the reviewer over one risk area at a time to collect rule candidates; estimate
  the token cost from the line count first and tell the user (`references/layer-5-ai-reviewer.md`, "Sweeps").
- **Do the gates pay for themselves?** → `node scripts/gate-report.mjs --rules <rule IDs>` summarizes
  `.quality-state/gate-log.jsonl` per week: blocks per gate and rule, rules that keep coming back
  (improve the message or design the mistake away), rules that never fired. Show it to the user.
- **New feature** → check passes, verify in the running app, update feature map + smoke, attach evidence.

## Running environment

- Tell the user the cost up front, with its source: in the benchmark (`docs/BENCHMARK.md`: a
  mid-size Remix app, Claude Code 2.1 and Codex CLI 0.169, November 2026) a setup took 24-32 minutes
  and about $6.5 of model usage with Claude Code, or about 8.5M tokens with Codex. Larger or
  multi-package projects cost more; say so rather than promising the benchmark figure.
- No display: never install or launch Cypress or desktop browsers. Headless Playwright is enough.
- Codex's sandbox blocks launching browsers; run the smoke flow outside the sandbox or leave it to CI.
- On small machines (8 GB RAM), run one heavy job at a time.
- Run long test suites in the background and read the result: a foreground tool call may be killed
  by the host after a couple of minutes and reported as if the user had interrupted.

## Pitfalls seen in practice

Read `references/lessons-learned.md` before Phase 3. The short list: check linter support for the
installed TypeScript major version; use isolated ports; pin browser locale; reset smoke state at the
start (and reset data); restart the verify stack after backend changes; make gate output show only
failures; prove checks fire; record console errors from a long-lived process; Codex Stop hooks must
print JSON on success; run linters from the repo root.
