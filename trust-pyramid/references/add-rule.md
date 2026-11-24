# Turning a correction into a rule

The workflow ships to every project as `quality/ADD_RULE.md` (from `templates/quality/ADD_RULE.md`),
so agents working in the project find it without this skill. Read that file and follow it. In short:

- After the task at hand, the agent asks the user whether it may add the rule and waits for a yes
  (the yes is also what Claude Code's auto-mode permission check needs before gate files are
  edited). Then it adds the rule itself and opens a pull request the user approves. A protected
  gate file is never a reason to wait for a maintainer.
- Work in a separate worktree, where the per-machine deny list does not apply:
  `git worktree add ../<repo>-rule-<name> -b rule/<name>`, then `bash scripts/check.sh --setup` there.
  Address its files by absolute path: permission rules check relative paths against the session's
  directory (the main checkout), where the deny list refuses them.
- Design the mistake away if possible; else a lint rule with a fix-it message, fixtures and a test;
  else a unit test or smoke step; else a row in `quality/rule-candidates.md` with a real reason.
- Prove the rule fires, remove the workaround, delete the ledger row, and explain the pull request
  in plain language.

Triggers: the user corrects a mistake; a gate, the reviewer or a bug report shows one; a ledger row
reaches `Seen` 2 (`scripts/hook-status.sh` and `scripts/gate-report.mjs` list those); a confirmed
reviewer finding has a `rule_candidate`.
