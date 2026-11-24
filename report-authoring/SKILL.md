---
name: report-authoring
description: Create complete, evidence-backed penetration-test reports with executive summaries, scope, methodology, risk-ranked findings, remediation guidance, and retest outcomes.
---

# Report authoring

## Purpose

Use this skill to convert reviewed assessment notes into a professional security report for technical teams, leadership and system owners. The output must distinguish verified facts, assumptions, tool observations and untested areas.

## Prerequisites

- The engagement owner has approved the report audience, distribution channel and classification.
- The assessment scope and test window are documented.
- Findings have been manually validated to the level permitted by the rules of engagement.
- Evidence has been reviewed and redacted.
- The severity model, CVSS version (if used), and report language are agreed.

## Inputs

Gather the canonical fields described in [the report data schema](../../templates/report-data.schema.json). Use [the example dataset](../../examples/report.example.json) only to understand the structure; it is fictional and must not be represented as a real assessment.

Collect:

- Engagement metadata and report classification.
- Executive summary and assessment objectives.
- Exact authorized scope, exclusions and test limitations.
- Methodology, relevant standards, tools and versions.
- Findings with stable identifiers, severity, confidence, status, affected assets, impact, evidence, reproduction steps and remediation.
- Prioritized recommendations and retest results.

## Workflow

### 1. Confirm audience and objective

Write for the people who must decide and remediate. An executive reader needs business context, exposure and priorities. An engineer needs technical evidence, affected component, preconditions, safe reproduction and a concrete fix.

### 2. Write the executive summary

Summarize what was assessed, why, what the evidence demonstrates and which actions matter most. Do not expose exploit details, internal secrets or personal data. Do not claim the environment is secure merely because no findings were identified.

### 3. Document scope and methodology

List the approved assets and exclusions precisely. State the assessment window and material constraints. Name standards and tools only when actually used; include versions where known. Describe meaningful gaps, inaccessible components and tests not performed.

### 4. Write each finding

Use [the finding template](../../templates/finding-report.md). Every finding should include:

- A unique, stable identifier and concise title.
- Severity and confidence, with a justified scoring vector if CVSS is used.
- Affected asset, component, endpoint, role or source location.
- A short summary and demonstrated impact.
- Preconditions and minimal reproduction steps.
- Redacted evidence tied to a timestamp and tool/version.
- Root cause where it is known.
- Specific remediation and a regression test.
- Relevant CWE, OWASP, WSTG or vendor references.
- Retest status and limitations.

Keep reproduction steps deterministic and non-destructive. Use synthetic test data and owner-provided accounts. Do not overstate the result or include steps that exceed the original authorization.

### 5. Prioritize recommendations

Rank recommendations by demonstrated risk, exposure, exploitability, data sensitivity, business impact, remediation complexity and compensating controls. Separate immediate containment from long-term improvements. Avoid false precision in severity scores.

### 6. Produce the canonical dataset

Populate a JSON file following [report-data.schema.json](../../templates/report-data.schema.json). A minimal command-line export is:

```bash
python scripts/report_builder.py \
  --input examples/report.example.json \
  --format markdown \
  --output /secure/reports/example-report.md
```

Replace the sample with a reviewed engagement dataset before any client deliverable is produced.

### 7. Coordinate review and sign-off

Have a technically qualified reviewer verify the evidence, scope and remediation. Obtain owner approval for distribution and classification. Do not send reports to recipients who are not explicitly approved.

## Quality rules

- No finding without evidence or a clearly stated confidence limitation.
- No CVSS score without a valid version, vector and rationale.
- No customer data, credentials, tokens, private keys or raw session material.
- No claims based solely on a product banner or an unverified scanner alert.
- No undocumented expansion beyond the engagement scope.
- Use consistent severity definitions and finding identifiers throughout.

## Output

A canonical structured report dataset ready for format-specific export, plus reviewer notes and an approved recipient list.

## Related skills

- [Evidence redaction](../evidence-redaction/SKILL.md)
- [Report quality assurance](../report-quality-assurance/SKILL.md)
- [Multi-format report export](../multi-format-report-export/SKILL.md)
- [Security reporting workflow](../security-reporting/SKILL.md)
