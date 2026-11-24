# Adding a rule

How a corrected mistake becomes a lint rule or a test in this project. **The agent proposes it and,
once the user agrees, does all of it** and opens a pull request; the user's part is to say yes and
to approve the pull request. Do not wait for a maintainer, and do not leave "maintainer to add" in
`quality/rule-candidates.md`.

## When

- The user corrects a mistake, or a gate, the reviewer or a bug report shows one.
- A row in `quality/rule-candidates.md` reaches `Seen` 2 (`bash scripts/hook-status.sh` lists them).
- A confirmed reviewer finding has a `rule_candidate`.

Finish the task at hand first. Then **ask** the user in one plain sentence and wait for the answer,
for example: "This is the second time X happened. May I add a rule for it in a separate worktree and
open a pull request for you to approve?" Do not create the worktree before they say yes: rule
changes edit gate files, and the user's yes is what authorizes that (in Claude Code's auto mode the
permission check looks for exactly this). On a yes, add it in the same session; on "not now", record
it in `quality/rule-candidates.md` and go on. Keep it out of the feature's pull request.

## Where: a separate worktree

Gate files (`lint/`, `scripts/`, the paths in `quality/protected-paths.txt`) are protected in this
checkout, so feature work cannot weaken them. Rules are added in a separate worktree on its own
branch, where that protection does not apply, and reach the default branch through a reviewed pull
request. A protected file is never a reason to stop: this is the route.

```bash
git worktree add ../<repo>-rule-<name> -b rule/<name>
cd ../<repo>-rule-<name> && bash scripts/check.sh --setup
```

**Use absolute paths for every file in the worktree**, in shell commands and in edit tools
(`/home/me/<repo>-rule-<name>/lint/...`, `C:/Users/me/<repo>-rule-<name>/lint/...`). Permission
rules check a relative path against the session's own directory, the main checkout, so
`cd ../<repo>-rule-x && sed ... lint/x.mjs` is refused there even though the file is in the worktree.
If an edit is still refused, tell the user what to run instead of looking for a way around it.

## Steps

1. **Name the mistake** in one sentence, with one real example (file:line).
2. **Can a design change make it impossible?** An API, a type, a wrapper or a directory boundary.
   If yes, make it, add a test that would catch a regression, and stop here.
3. **Visible in the source?** Write a lint rule:
   - the message says what is wrong, how to fix it, and the sanctioned exception;
   - a violating and a compliant fixture, and a test that asserts the rule ID and line;
   - run it on the whole repository; fix existing violations, or limit the rule to new code and
     record the backlog in `quality/rule-candidates.md`.
4. **Only visible at runtime?** Add a unit test or a smoke step instead.
5. **Neither?** Add or bump the row in `quality/rule-candidates.md` with a real reason (see its header).
6. **Prove it fires:** break one compliant example on purpose, see the rule fail, restore it.
7. **Clean up:** remove the workaround that led to the mistake, so agents stop copying it, and
   delete the row from `quality/rule-candidates.md` if it had one.
8. Run `bash scripts/check.sh --all`, commit, push, and open a pull request.

## The pull request

Title: `rule: <the mistake, in a few words>`. In plain language, for a reader who does not read code:

- the mistake, with the real example, and how often it happened;
- what now stops it (the rule's error message, quoted);
- that CI marks the change as touching protected files, which is expected for a rule;
- what the user should check before approving: does the rule describe a real mistake in this
  project, and does the error message say clearly what to do instead.

Then remove the worktree: `git worktree remove ../<repo>-rule-<name>`.
