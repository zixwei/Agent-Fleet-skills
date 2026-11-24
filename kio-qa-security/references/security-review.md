# 🛡️ Security Review: Two Stages, Five Layers, Sign-off

Written by Kio (© 2026 Toan Pham / Kio System, MIT License). It covers the review process only. Automated scanning (Semgrep, CodeQL, SARIF merging) lives in [`codeql/SKILL.md`](codeql/SKILL.md), which is adapted from Trail of Bits and licensed **CC BY-SA 4.0**, not MIT.

## 1. Two-stage code review

**Stage 1 — spec compliance.** Does the change satisfy every acceptance criterion in the PRD/issue? Flag missing cases and unrequested scope.

**Stage 2 — code quality and security.** Run the 5-layer checklist below and, for risky diffs, a scan.

Receiving feedback: verify each comment against the codebase before changing anything, push back with evidence when a suggestion is wrong, and fix one issue at a time with tests after each.

## 2. The 5-layer quality checklist

1. **Security & zero trust** — parameterised queries only; output escaped for its context (HTML, attribute, URL, JS); CSRF tokens/nonces and capability checks on every state-changing endpoint; no secrets in code; public forms protected by honeypot, minimum fill time (< 3 s rejected), per-IP rate limit and an invisible challenge (Turnstile / reCAPTCHA v3).
2. **Performance & resources** — no queries inside loops (N+1); handles, sockets and connections closed in `finally`; no repeated heavy allocation in hot loops.
3. **Concurrency** — multi-step mutations inside transactions; webhooks and payment handlers idempotent.
4. **Architecture** — files under 700 lines; no `TODO` stubs or mock data on production paths.
5. **Project rules** — follow the repository's `CLAUDE.md` / `AGENTS.md` / `rules/GLOBAL_RULE.md`.

## 3. Line-level diff review (optional)

If the open-source `ocr` CLI (Alibaba open-code-review) is installed:

```bash
ocr review --audience agent -b "Spec compliance and security audit for latest changes"
ocr review --base main
```

Spend effort on `critical` and `high` findings; drop style nitpicks that conflict with intentional design.

---

## 4. Automated scan (SAST)

For risky diffs or a pre-release audit, run Semgrep and/or CodeQL by following [`codeql/SKILL.md`](codeql/SKILL.md). Triage every finding before signing off.

## Sign-off format

```markdown
[🛡️ Kio Security Review — Sign-off]
1. Two-stage review: PASS — matches spec, quality review done
2. 5-layer checklist: PASS — 0 SQLi/XSS, 0 secrets, files < 700 lines, 0 TODO
3. Diff review: PASS — 0 critical/high
4. SAST (Semgrep/CodeQL): PASS — <n> findings triaged, 0 open high/critical
Evidence: <commands run, SARIF paths>
```
