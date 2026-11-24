---
name: report-quality-assurance
description: Perform a pre-delivery quality review of security reports for evidence accuracy, scope compliance, severity consistency, redaction, remediation usefulness and export integrity.
---

# Report quality assurance

## Purpose

Use this skill before a report leaves the assessment team. It provides a repeatable peer-review gate for narrative reports and generated files.

## Prerequisites

- A canonical JSON report dataset exists.
- [Report authoring](../report-authoring/SKILL.md) is complete.
- [Evidence redaction](../evidence-redaction/SKILL.md) has been performed.
- The report classification, recipients and delivery channel are approved.

## Workflow

### 1. Scope and authorization review

Confirm the report names only authorized assets and that all testing described occurred within the agreed window and limits. Verify the exclusions and limitations are visible. Remove details of out-of-scope systems unless an approved disclosure requires them.

### 2. Finding evidence review

For each finding, ask:

- Is the issue manually validated, or clearly marked as unverified?
- Does the evidence support the claim and the stated impact?
- Are the reproduction steps minimal, reproducible and within the authorization?
- Is the affected asset and role precise?
- Are assumptions and limitations clearly stated?
- Is the finding distinct from duplicates?

Scanner output alone is not a confirmed finding.

### 3. Severity and consistency review

Confirm that the severity model is stated and applied consistently. For CVSS, verify the version, vector, calculated score and rationale. Do not treat CVSS as a business-risk score or fabricate an exact score where the necessary information is missing.

Check finding IDs, severity labels, status, asset identifiers and counts across the executive summary, detailed findings, remediation table and machine-readable exports.

### 4. Content and remediation review

Check grammar, clarity, terminology, references, timestamps and tool versions. Each high-priority finding should have a practical remediation action and a way to verify the fix. Ensure the executive summary does not make unsupported claims about overall security.

### 5. Redaction review

Run [the evidence redaction workflow](../evidence-redaction/SKILL.md). Check visible content and metadata. Ensure passwords, keys, access tokens, cookies, private URLs, customer records and internal-only details are not disclosed to unapproved recipients.

### 6. Validate the canonical data

Run the built-in exporter against the JSON source. This validates the minimum data requirements before generating an output:

```bash
python scripts/report_builder.py \
  --input report.json \
  --format json \
  --output /secure/review/report-validated.json
```

Where an external JSON Schema validator is available, validate against [report-data.schema.json](../../templates/report-data.schema.json) as an additional check.

### 7. Inspect every export

For every requested format:

- Markdown: check headings, lists, code/text and references.
- HTML: view in a browser and confirm all supplied text is rendered as text, not executable markup.
- DOCX: inspect styles, page breaks, headers/footers, tables, page numbers and document properties.
- PDF: inspect fonts, clipping, links, pagination, searchable text and applied redactions.
- CSV: confirm one row per finding and safe spreadsheet cell values.
- JSON: parse the file and compare record counts and IDs.
- SARIF: validate the SARIF structure and confirm source locations are present only when supplied.

### 8. Approve or reject release

Mark the report as Approved only when scope, evidence, severity, redaction, content and export checks are complete. Otherwise return a list of blocking corrections. Record reviewer, review date, approved version and recipients.

## Release checklist

- [ ] Scope and authorization are accurate.
- [ ] Every finding is validated or marked with its confidence/limitations.
- [ ] Severity and CVSS values are justified.
- [ ] Reproduction steps are safe and reproducible.
- [ ] Remediation and retest criteria are actionable.
- [ ] Sensitive content and metadata have been checked.
- [ ] Executive summary, findings and exports are consistent.
- [ ] Every generated file was opened and visually inspected.
- [ ] Classification and approved recipient list are correct.
- [ ] Secure delivery and retention requirements are recorded.

## Output

A reviewed report version, an approval/rework decision, and a completed QA checklist.
