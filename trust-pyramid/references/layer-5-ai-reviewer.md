# Layer 5 - The AI reviewer (enforced, evidence-based)

Early on there are few rules and the human may not be able to spot problems. An AI reviewer finds
candidates; **evidence, not the reviewer's opinion, decides**; confirmed problems become rules.

**When to run it:** once per pull request in CI (both profiles). Running it after every task (full
profile only) found nothing extra with a frontier model in benchmarks and added cost; it is worth it
with smaller models or high-risk code.

## Two ways a reviewer layer gets skipped, and the fix for each

| Failure | Fix |
|---|---|
| The reviewer never runs (agent forgets or decides it is unnecessary) | Run it in CI on every PR and make `review-gate` a required status check. The checked agent does not trigger it. |
| It runs, but findings are waved away ("can't prove it, just a suggestion") | `review-gate` requires a disposition for every finding and machine-checks confirmed ones: the named test must fail on the base commit and pass on the PR head. Rejections need a reason and are shown to the human. |

Forcing the reviewer to run does not make it right. It guarantees that every change was looked at
and every finding was accounted for. Its long-term value is the rules it produces.

## Files

| Template | Installed at | Purpose |
|---|---|---|
| `quality/reviewer-prompt.md` | `quality/reviewer-prompt.md` | single source of the reviewer instructions |
| `claude/agents/quality-reviewer.md` | `.claude/agents/quality-reviewer.md` | local reviewer subagent for Claude Code |
| `scripts/review-gate.mjs` | `scripts/review-gate.mjs` | deterministic gate over findings + dispositions |
| `quality/review.config.json` | `quality/review.config.json` | install/test commands used for evidence |
| `quality/review-dispositions.json` | committed per PR | the agent's answer to each finding |
| `quality/rule-candidates.md`, `quality/review-log.md` | same | the ratchet ledger |

Codex has no project subagent file in this setup; run the reviewer locally with a fresh session:
`codex exec "$(cat quality/reviewer-prompt.md)"` (or start a new thread and paste the prompt).

## Reviewer rules (summarized; the prompt has the full text)

- Fresh context. Input: the diff (or a path for sweeps), `AGENTS.md`, feature map, rule list.
  Never the author's reasoning or the human's suspicions.
- Read-only tools.
- Focus: money/quota, permissions and data isolation, secrets, unvalidated model output,
  concurrency and double submits, swallowed errors, time zones and day boundaries, and patches that
  treat symptoms instead of root causes.
- Output strictly JSON: `[{file, line, severity, summary, failure_scenario, proof}]` where `proof`
  says which failing test or which verify-CLI reproduction would demonstrate it.
- Prefer a different model from the author, or at least a different prompt.

## Dispositions (written by the authoring agent)

`quality/review-dispositions.json`:

```json
{
  "findings": {
    "a1b2c3d4": {
      "status": "confirmed",
      "testFiles": ["server/test/checkout.test.ts"],
      "testCommand": "npm --prefix server test -- test/checkout.test.ts"
    },
    "e5f6a7b8": {
      "status": "rejected",
      "reason": "The webhook already deduplicates by event id (stripe_events), see server/src/webhook.ts:88."
    },
    "c9d0e1f2": {
      "status": "deferred",
      "reason": "Real, but needs a schema migration; tracked as rule candidate c9d0e1f2."
    }
  }
}
```

Finding IDs are computed by `review-gate.mjs` from `file` + `summary` and printed in its output and
in the PR comment.

`review-gate.mjs` fails when:
- a finding has no disposition;
- a rejection or deferral has a reason shorter than 20 characters;
- a deferred finding's ID does not appear in `quality/rule-candidates.md` (deferred means tracked,
  not forgotten; "rejected" is only for findings that are not real problems);
- a confirmed finding's tests do not **fail** when copied onto the reviewed commit, or do not
  **pass** on the head commit (red → green proof);
- the base run failed for the wrong reason - an import error, a missing symbol, a syntax error -
  instead of at an assertion. Write the evidence test against an API that already exists on base.

The "red" side is the commit the reviewer looked at, stored as `reviewed_sha` in `review.json`
(the CI job writes it). This matters: a bug the PR itself introduced does not exist on `main`, so a
test copied onto `main` could only fail for the wrong reason. Without `reviewed_sha` the gate falls
back to `$REVIEW_BASE_REF`, then `origin/main`, then `main`. Pass `--base <ref>` to override.
Whatever the source, the red-side commit must exist, be an ancestor of HEAD, and not be HEAD itself
(fixes go in new commits on top of the reviewed one); the gate prints which commit it used and why.

Locally, record it yourself after a review:
`{ "reviewed_sha": "<git rev-parse HEAD at review time>", "findings": [ ... ] }`.

The review is cached by a hash of the code diff that excludes `quality/review-dispositions.json`,
so committing dispositions does not trigger a new review; changing code does.

## After a finding is confirmed (the ratchet)

1. Fix it; the evidence test turns green.
2. Decide, starting from the finding's `rule_candidate`: lint rule (`quality/ADD_RULE.md`) / smoke
   step or unit test / neither. If neither, add or bump an entry in `quality/rule-candidates.md`.
   The **second** occurrence of the same kind must become a rule or test, which the agent adds
   itself through a pull request.
3. Append to `quality/review-log.md`: scope, confirmed, rejected, rules/tests added.

## Sweeps

Weekly or per milestone, run the reviewer over a scope of the repo (`proof` may be "pattern seen N
times"). Goal: rank rule candidates, not fix everything now.

**Estimate the cost first and tell the user.** Count the lines in scope
(`git ls-files -- <paths> | xargs wc -l | tail -1`) and estimate about 25 tokens per line. Observed
on a real project: one sweep of about 10k lines (server code, security rules, client services) used
about 250k tokens and 43 tool calls. Ask before a sweep that would exceed the user's budget.

**Scope by risk, and repeat what matters.** Sweep money, permissions and personal data first, one
area per run, rather than the whole repo at once. One run says little about stability: the same
sweep found three real problems the author had not seen and missed two known ones in the same
scope. When the result matters, run the same scope two or three times (ideally with a different
model or a fresh session) and merge the findings.

**Record every run** in `quality/review-log.md`: scope, files read, tokens (or cost), findings and
their dispositions, and any known problem in scope that was missed. Over time this shows the
reviewer's recall per area and whether repeated runs are worth their cost.

## Plain-language summary for the human

For each confirmed finding: what a user would experience, the evidence (test name red → green or
screenshots), and the rule/test added. For each rejection: the reason, so the human can disagree.

## Security notes

- The reviewer reads untrusted PR content: keep its tools read-only, never give it write tokens,
  and do not interpolate PR titles/bodies into shell commands.
- Secrets are unavailable to workflows from forks; the review job skips and `review-gate` fails
  closed with a clear message until a maintainer runs it.
