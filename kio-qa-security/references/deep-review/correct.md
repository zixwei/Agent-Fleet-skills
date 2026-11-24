
# Correct

> pstack skill (MIT, see `PSTACK-NOTICE.md`), adapted for Claude Code (user-level, generic).

The operator keeps correcting agents in this repo for the same mistakes. Change the repo so the next agent can't make them.

Assume every contributor is an agent that sees only the files it opened, copies the nearest example, and takes the shortest path that compiles. Design the repo so a change that looks right from one file is right for the whole repo.

## Find the mistake classes

First, read recent commits, reverts, review comments, agent instruction files, and comments that explain workarounds. Group the mistakes into classes. A class counts once it has happened twice.

Sources: `git log` (reverts, restores, "fix … #n", repairs of earlier changes), review comments (`gh pr view <n> --comments`), the project's instruction files, its rule table if it has one, and the user's memory notes in `~/.claude/projects/<project-slug>/memory/` (`type: feedback` notes are the user's corrections). The evidence pass is read-only and can go to a cheaper subagent.

## Fix each class at the highest level that works

1. **Eliminate it with architecture.** Give each piece of state one owner and each task one supported way. Hide internals so the wrong import fails. Replace hand-synced lists with one source of truth. Delete old ways and dead code an agent would copy.
2. **Enforce it with types so the bad state can't be written.** If bad code still compiles, add a lint or check whose error names the file, type, or function to use instead. If the pattern is already common, fail only when a change adds more.
3. **Test the behavior.** Fix or delete any test that still passes when the behavior it checks breaks, or that fails when only the internals change.
4. **Write docs or agent rules last, only for judgment calls.** Nothing fails when an agent skips them.

## Fix and prove

Then fix the most frequent classes, one PR each (or one commit each in one PR). Prove each new check fails on a real past mistake: reproduce the old bad state (for example check out the bad commit in a scratch worktree) and paste the failing output. Exceptions go on the offending line with a reason, an expiry date, and a human's approval.

Follow the project's own change process (branch base, who writes code, PR rules) from its instruction file. If CI doesn't run on every PR, every check must also run locally through one command (a make target or script), and its output goes in the PR body.

## Keep the rule table

Last, keep the rule table where the project keeps it (for example `docs/rules.md`, linked from the instruction file; create it there if there is none), pairing each rule with what enforces it. When the operator corrects you, fix the mistake and add the rule. If the rule was already there and nothing enforces it, that's a repeat, so fix it at the highest level in the same change. Drop a rule once its mistake can't happen.

**Reply:** each class with its evidence, the level you picked, and why a higher level didn't work.
