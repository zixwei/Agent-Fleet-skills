---
name: review-local
description: >-
  Review your own local changes before you commit or push: uncommitted edits,
  staged changes, or your branch against its base. Checks requirement fit,
  behavior changes, correctness, regressions, and security, runs the project's
  checks, and reports verified findings with fixes. Use for "review my changes",
  "review before I push", "review staged", "check my diff", or "review again".
---

# Review local changes

Review the developer's own change the way a careful teammate would, before anyone else sees it. Support each finding with evidence, and help fix what you find when asked.

Start with a one-line update naming what you will review.

## 1. Pick the target

- **Named by the user:** staged changes, specific files, a commit range, or a branch against a base. Use exactly that.
- **Nothing named:** match the intent. Before a commit, review the index. Before a push, review outgoing commits (`@{push}..HEAD`, or the merge base with the default branch) plus any uncommitted edits, listed separately. Otherwise, review the working tree against `HEAD` plus relevant untracked files. Say which target and base you chose.
- Compare staged changes against `HEAD` and unstaged changes against the index when the user separates them.

Pin the base commit before reading code. Never stage, commit, stash, reset, or switch branches.

## 2. Understand intent and trace behavior

Take the requirement from the user, then from the branch name, commit messages, and any linked ticket. If none exists, infer it from the code and label it as inferred. Load repository guidance (`CONTRIBUTING.md`, `CLAUDE.md`, `AGENTS.md`, `REVIEW.md`, lint and test config).

Read every changed line, then trace affected callers, helpers, consumers, and data paths. Compare old and new behavior for representative inputs and failure paths. Check:

- **Contracts and control flow:** inputs, defaults, outputs, errors, removed guards, early returns, side effects, event consumers, existing data. Check callers for unsafe dereferences when a helper catches an error and returns null, or when ORM eager loading switches a field between an ID and an object. Trace renamed fields and new statuses through readers such as reports and exports. For money, check the authority for each amount, currency, units, and rounding.
- **State and rollout:** partial failure, retries, duplicate delivery, concurrent writes, multi-instance jobs, migrations, and old and new versions running side by side. Trace retries from partially written state.
- **Frontend:** late responses overwriting newer state, loading and error handling, keyboard and focus regressions.
- **Security and privacy:** authorization and tenant boundaries, injection (SQL/NoSQL, shell, templates, XSS, XXE, path traversal), SSRF, TLS verification, CORS, unsafe defaults, signatures verified before input normalization, secrets and sensitive logs, dependency risk. Run a redacted secret scan on the change when a scanner is installed. Before a push, scan each outgoing commit, not just the final diff.
- **Tests and conventions:** coverage for new behavior, failure paths, and regressions. Check edited assertions against intended outcomes and flag meaningful deviations from repository conventions.
- **Commit hygiene:** leftover debug output, focused or skipped tests, conflict markers, accidentally added files, and staged code that depends on untracked or unstaged files.

## 3. Run checks and verify findings

Run the project's own type-check, lint on changed files, and tests near the changed code, using the repository's scripts. Run checks against the version under review. When the working tree matches it (e.g. the default working-tree review), run in place. Otherwise use a temporary worktree (`git worktree add --detach <tmp> <commit>`); for a staged review, build the patch in the original checkout with `git diff --cached --binary` and apply it there. Remove the worktree afterwards. If it lacks dependencies, review statically. When running in place, read the script definitions and avoid ones that fix, format, or update snapshots. Compare `git status --porcelain` and `git diff HEAD --binary | sha256sum` before and after, and report any file a check changed. See [language notes](references/languages.md) when the project gives no clear starting point. Don't install or upgrade dependencies or change config to make a check pass. If a check fails, check whether it also fails on the base before blaming the change. Report checks that couldn't run, with the reason.

For a correctness finding, a focused temporary test against the real code can show expected and actual behavior. Delete it afterwards.

Before reporting each finding:

1. Confirm the file and line in the version under review (the index for a staged review). For removed code, cite the diff or the base line.
2. For a bug, name the triggering input or state, the reachable path, and the observable impact. Look for guards, config, constraints, or callers that disprove it. Drop it if you can't. For an unmet requirement or a convention violation, cite the criterion or repository rule and show where the change diverges.
3. Note whether the change introduced it. Keep pre-existing issues separate; they don't affect the verdict.
4. Give the smallest practical fix.

## 4. Report

Use the user's format, or:

```markdown
## Verdict
<Ready to push | Fix first | Not ready> — <scope and reason>

## What changed
<Plain-language before → after>

## Requirement fit
<Met, partial, unmet, or unverified, with source>

## Findings
<Must fix / Should fix / Nit: title, file:line, evidence, fix>

## Checks
<What ran and the result, what couldn't run and why>
```

**Must fix** blocks pushing, **Should fix** is recommended before pushing, **Nit** is optional. Use **Fix first** when there is a Must fix or Should fix finding, **Not ready** when an evidence gap prevents a judgment, and **Ready to push** otherwise. Say so when nothing actionable was found.

## 5. Fix when asked

Apply fixes only when the user asks, one finding at a time or as a batch they choose. Re-run the checks that cover each fix. For "review again", keep the earlier target and base unless the user changes them, review the new diff, and mark earlier findings as fixed, partly fixed, or open.
