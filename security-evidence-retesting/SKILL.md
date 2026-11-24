---
name: security-evidence-retesting
description: Manage defensible security assessment evidence, finding validation, remediation retests and closure decisions with provenance, integrity hashes, redaction, retention and reproducible test records.
---

# Security evidence and retesting

## Purpose

Create an evidence lifecycle that connects observations to validated findings, remediation plans and retest outcomes. Evidence must be minimal, reproducible, integrity-protected and suitable for technical review without exposing unrelated sensitive data.

## Prerequisites

- Engagement authorization, scope, classification, retention and evidence-sharing requirements are known.
- [Evidence redaction](../evidence-redaction/SKILL.md), [Security reporting](../security-reporting/SKILL.md) and [Report quality assurance](../report-quality-assurance/SKILL.md) are available.
- The evidence owner and recipients are approved. Establish restricted, encrypted storage with least-privilege access.
- Confirm legal hold, client retention, deletion and disclosure requirements before collecting artifacts.

## Evidence lifecycle

### 1. Create an evidence plan

Define evidence IDs, finding IDs, collection methods, source assets, owner, allowed content, file-size limits, retention expiry and redaction requirements. Avoid collecting full databases, memory dumps, packet payloads or complete home directories when a focused artifact can demonstrate the condition.

### 2. Capture provenance

For each item record:
- Evidence ID, engagement ID and linked finding/test ID.
- Source asset and exact build/version/configuration, redacted as required.
- Collector, collection tool/version, timestamp with timezone and method.
- Original artifact name, media type, size and SHA-256 digest.
- Storage path/reference, classification and access list.

Preserve the original only in approved restricted storage. A hash proves integrity relative to the captured file, not that the observation is true or authorized.

### 3. Validate evidence quality

Check completeness, reproducibility, timestamps, sample identity, context and whether the evidence supports the claim. Correlate with a second independent source where appropriate. Distinguish raw tool output, analyst interpretation and confirmed impact.

### 4. Redact and prepare derivatives

Use the existing evidence-redaction workflow. Remove tokens, credentials, private keys, session identifiers, customer content, unrelated hostnames and personal data. Verify that redaction is irreversible and that document metadata, hidden layers, comments and embedded objects do not preserve the original values.

### 5. Track finding status

Use controlled statuses such as Observed, Needs Validation, Confirmed, Accepted Risk, False Positive, Remediated, Retest Pending, Retest Passed, Retest Failed and Not Retested. Record confidence separately from severity. Do not mark a finding closed solely because a scanner no longer reports it.

### 6. Prepare a retest plan

For every remediation, record the original finding ID, exact affected asset/build, remediation reference, fixed version/configuration, retest authorization/window and a repeatable test procedure. Retest only the original scope and minimum steps needed to verify the control. If the asset or technique is no longer authorized, mark Not Retested.

### 7. Execute the retest

Use the same test identity class, environment, control and relevant test conditions. Prefer a patched build or configuration snapshot. Record tool versions and expected/observed behavior. Confirm that the remediation did not create a different exposure and that the original issue is not merely masked by a changed test condition.

### 8. Decide closure

- **Retest Passed:** the original condition is no longer reproducible and the intended security control is present.
- **Retest Failed:** the original condition remains or the fix is incomplete.
- **Partially Remediated:** risk is reduced but the original control requirement is not fully met.
- **Not Retested:** no authorized/reproducible test was performed.
- **Accepted Risk:** owner-approved acceptance with approver, rationale, expiry and compensating controls.

Record residual risk and do not silently downgrade severity.

### 9. Finalize the evidence package

Generate an evidence index, integrity manifest, finding-to-evidence mapping, retest results, redaction log and retention/deletion schedule. Re-run report quality checks before approved delivery.

## Output

An evidence register, SHA-256 integrity manifest, collection provenance, redacted derivatives, finding lifecycle and retest status, closure decisions, residual-risk register and retention record.

## Safety constraints

- Do not collect more data than necessary or preserve credentials/secrets as proof.
- Do not alter original evidence after capture; make redacted derivatives and retain chain-of-custody notes.
- Do not retest outside the current authorized scope or after authorization expires.
- Do not close findings based only on scanner disappearance or owner assertions.
- Securely delete evidence at the approved expiry unless retention is legally required.

## References

- [Pentestizer Evidence Redaction](../evidence-redaction/SKILL.md)
- [Pentestizer Security Reporting](../security-reporting/SKILL.md)
- [Pentestizer Report QA](../report-quality-assurance/SKILL.md)
- [NIST SP 800-86 — Guide to Integrating Forensic Techniques into Incident Response](https://csrc.nist.gov/pubs/sp/800/86/final)
- [NIST SP 800-101 Rev. 1 — Guidelines on Mobile Device Forensics](https://csrc.nist.gov/pubs/sp/800/101/r1/final)
