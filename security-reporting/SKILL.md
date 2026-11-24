---
name: security-reporting
description: Coordinate the end-to-end security reporting workflow from validated findings and evidence through authoring, redaction, multi-format exports, quality assurance and approved delivery.
---

# Security reporting

## Purpose

Use this skill as the entry point whenever a penetration test or security assessment needs a formal deliverable. It orchestrates the focused reporting skills rather than duplicating their detailed procedures.

## Prerequisites

- A current engagement scope and rules of engagement exist.
- Observations have been triaged and findings manually validated where possible.
- The report audience, classification, recipient list and delivery channel are approved.
- Evidence handling and retention requirements are known.

## End-to-end workflow

1. **Prepare the source data.** Collect the assessment metadata, exact scope, exclusions, methodology, validated findings, evidence references, remediation and retest state. Follow [Report authoring](../report-authoring/SKILL.md).
2. **Normalize the dataset.** Use [the report data schema](../../templates/report-data.schema.json) and the fictional [example dataset](../../examples/report.example.json) as a structural reference only.
3. **Redact evidence.** Follow [Evidence redaction](../evidence-redaction/SKILL.md). Never place secrets, private keys, access tokens, session cookies or personal/customer data into the export dataset.
4. **Export requested formats.** Follow [Multi-format report export](../multi-format-report-export/SKILL.md) and use `scripts/report_builder.py`.
5. **Perform QA.** Follow [Report quality assurance](../report-quality-assurance/SKILL.md). Open each exported file and compare finding IDs, severity, counts, metadata and redactions across formats.
6. **Deliver securely.** Obtain reviewer/owner approval and distribute only to authorized recipients. Record the report version, delivery date and retention/deletion requirements.

## Available export formats

| Format | Use |
|---|---|
| Markdown | Review, version control and future conversion |
| HTML | Standalone browser-friendly report |
| JSON | Structured archive and automation |
| CSV | Finding register and remediation tracking |
| SARIF 2.1.0 | Static-analysis/code-scanning integrations |
| DOCX | Editable Word deliverable via optional Pandoc |
| PDF | Fixed-layout deliverable via Pandoc and a PDF engine |

SARIF is not a replacement for the narrative penetration-test report. It is most useful when findings include source-code locations and can be ingested by compatible development tools.

## Minimal export example

```bash
python scripts/report_builder.py \
  --input report.json \
  --format html \
  --output /secure/reports/security-assessment.html
```

For all commands and optional reporting platforms, see [Report Generation](../../docs/reporting.md).

## Quality rules

- Never report an unverified scanner alert as a confirmed vulnerability.
- Explain the evidence, confidence, impact, affected asset, prerequisites and limitations.
- Use a consistent severity model. Include a CVSS version, vector and rationale whenever a score is published.
- Remove sensitive text and metadata; verify that redaction is irreversible.
- Keep narrative and machine-readable exports consistent.
- Never claim that report generation or export itself validates the security finding.
- Deliver only through the agreed secure channel.

## Output

An approved report in the requested formats, its canonical JSON source, redacted evidence, completed QA checklist and delivery record.

## Related skills

- [Report authoring](../report-authoring/SKILL.md)
- [Evidence redaction](../evidence-redaction/SKILL.md)
- [Multi-format report export](../multi-format-report-export/SKILL.md)
- [Report quality assurance](../report-quality-assurance/SKILL.md)
