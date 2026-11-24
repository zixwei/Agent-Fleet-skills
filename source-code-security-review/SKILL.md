---
name: source-code-security-review
description: Review an explicitly authorized source repository for insecure code patterns, vulnerable dependencies, exposed secrets and configuration weaknesses using local static analysis tools.
---

# Source code security review

## Purpose

Use this skill when the engagement explicitly includes source code, build files or dependency manifests. Prefer local, read-only analysis and coordinate findings with the maintainers.

## Prerequisites

- Written authorization identifies the repository, commit or branch and permitted analysis.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The source owner has approved use of external analysis services, if any.
- A clean, isolated working copy is available.
- Sensitive files and secrets are handled according to the engagement agreement.

## Tools

- **Semgrep:** static analysis — [documentation](https://semgrep.dev/docs/).
- **Gitleaks:** secret detection — [repository](https://github.com/gitleaks/gitleaks).
- **Trivy:** dependency, filesystem and configuration scanning — [documentation](https://trivy.dev/latest/docs/).

Install from official sources and record exact versions. Do not upload private source code to hosted services without explicit owner approval.

## Workflow

### 1. Confirm the code boundary

Record repository URL, owner, commit SHA, branch, submodules and generated/vendor directories included or excluded. Confirm whether secrets and deployment configuration are part of the approved review.

### 2. Understand the application

Identify languages, frameworks, build systems, entry points, authentication and authorization boundaries, data stores, network interfaces and deployment artifacts. Use maintainers' architecture notes where available.

### 3. Run local static analysis

For Semgrep, choose rules appropriate to the languages and project and review the configuration before execution. A starting command is:

```bash
semgrep scan --config auto --metrics=off .
```

Rules may produce false positives. Review code paths and data flow before classifying a result as a vulnerability.

### 4. Check for exposed secrets

Run Gitleaks against the approved working copy:

```bash
gitleaks dir . --redact
```

Never copy detected secret values into a report. If a live secret is found, notify the owner to revoke or rotate it through their approved process. Do not test the credential against external services.

### 5. Review dependencies and configuration

Run Trivy filesystem checks where permitted:

```bash
trivy fs --scanners vuln,misconfig,secret .
```

Review package manifests and lockfiles, container definitions, infrastructure-as-code files, CI pipelines and unsafe defaults. Keep database updates and network lookups within the owner's approved data-handling boundaries.

### 6. Manually validate important findings

Inspect the relevant code path, reachability, configuration, dependency usage and runtime conditions. Distinguish a vulnerable package being present from vulnerable code being reachable. Confirm that secrets are active only through an owner-led rotation process.

### 7. Report actionable remediation

Include file path and line range, commit SHA, rule ID, dependency/version evidence, confidence and a specific mitigation. Redact secret values and sensitive configuration. Do not commit fixes to the owner's repository unless remediation work is separately authorized.

## Output

A reviewed findings list covering code patterns, dependency exposure, secrets and configuration weaknesses, with false positives and scan limitations identified.

## Safety constraints

- Do not execute untrusted project code or install dependencies with lifecycle scripts without review and approval.
- Do not use found credentials, tokens or keys.
- Do not run malware, persistence or exploit payloads.
- Do not expose private source code or findings to third-party services without approval.
- Stop if the repository or commit is not within the approved engagement.

## References

- [Semgrep documentation](https://semgrep.dev/docs/)
- [Gitleaks](https://github.com/gitleaks/gitleaks)
- [Trivy documentation](https://trivy.dev/latest/docs/)
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/)
