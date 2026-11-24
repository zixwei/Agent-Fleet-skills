---
name: multi-format-report-export
description: Export reviewed Pentestizer report data into Markdown, HTML, JSON, CSV, SARIF, DOCX, or PDF using safe local rendering and optional Pandoc conversion.
---

> 边界:仅限授权测试。只用于已获书面授权的渗透测试目标。

# Multi-format report export

## Purpose

Use this skill after report data has been authored and reviewed. Export the same canonical JSON report into one or more formats for human reading, editing, archiving or integration with security tooling.

## Supported formats

| Format | Generator | Typical use |
| --- | --- | --- |
| Markdown (.md) | Built-in | Version control, review and future conversion |
| HTML (.html) | Built-in, escaped output | Browser-friendly standalone report |
| JSON (.json) | Built-in | Structured archive, automation and integrations |
| CSV (.csv) | Built-in, spreadsheet-safe cells | Finding registers and remediation tracking |
| SARIF (.sarif) | Built-in SARIF 2.1.0 output | Code-scanning and static-analysis integrations |
| DOCX (.docx) | Pandoc (optional) | Editable Microsoft Word deliverable |
| PDF (.pdf) | Pandoc and a supported PDF engine (optional) | Fixed-layout client deliverable |

SARIF is primarily intended for exchanging static-analysis results. Use it for code findings that have suitable source locations and tool metadata; do not treat it as a complete replacement for a narrative penetration-test report. See the [OASIS SARIF 2.1.0 standard](https://docs.oasis-open.org/sarif/sarif/v2.1.0/os/sarif-v2.1.0-os.html).

## Prerequisites

- Report data follows [the canonical schema](../../templates/report-data.schema.json).
- Findings and evidence have passed [report quality assurance](../report-quality-assurance/SKILL.md).
- Sensitive evidence has passed [redaction review](../evidence-redaction/SKILL.md).
- The report owner has approved the format, recipients, classification and output location.
- For DOCX/PDF, Pandoc is installed locally and its PDF engine is configured where required.

## Built-in exports

The report builder uses only the Python standard library for Markdown, HTML, JSON, CSV and SARIF output.

```bash
python scripts/report_builder.py --input report.json --format markdown --output out/report.md
python scripts/report_builder.py --input report.json --format html     --output out/report.html
python scripts/report_builder.py --input report.json --format json     --output out/report.json
python scripts/report_builder.py --input report.json --format csv      --output out/findings.csv
python scripts/report_builder.py --input report.json --format sarif    --output out/findings.sarif
```

The HTML renderer escapes report values as plain text and does not interpret report-provided HTML. The CSV renderer prefixes common spreadsheet formula starters to reduce formula-injection risk. These protections do not replace final review of the generated files.

## DOCX export

Install Pandoc from its [official installation guide](https://pandoc.org/installing.html). Export with the optional reference document to apply the client's approved Word styles:

```bash
python scripts/report_builder.py \
  --input report.json \
  --format docx \
  --output out/security-assessment.docx \
  --reference-docx templates/reference-report.docx
```

The reference DOCX option is optional. Pentestizer does not currently ship a branded binary reference template; provide an approved local reference file when a particular layout is required.

## PDF export

Pandoc requires a supported PDF engine. Install and configure one according to the current [Pandoc PDF documentation](https://pandoc.org/MANUAL.html#creating-a-pdf). Example using Tectonic:

```bash
python scripts/report_builder.py \
  --input report.json \
  --format pdf \
  --output out/security-assessment.pdf \
  --pdf-engine tectonic
```

Verify fonts, page breaks, tables, page numbers, links, metadata and redactions in the final PDF. Do not distribute a PDF merely because the command succeeded.

## Existing report platforms

For teams that need collaboration, reusable finding libraries, report design or project management, consider these separate platforms:

- [PwnDoc](https://github.com/pwndoc/pwndoc): open-source penetration-test reporting application with collaborative workflows and customizable DOCX templates.
- [SysReptor](https://docs.sysreptor.com/): pentest report platform with HTML report designs and PDF publishing; its documentation states that DOCX export is not supported. Self-hosted Markdown export is available through its plugin.
- [reptor CLI / Python library](https://docs.sysreptor.com/cli/getting-started/): automate project and finding operations with SysReptor.

These products are optional integrations/workflows, not dependencies of the built-in report builder. Review their licensing, hosting and data handling before using them with client information.

## Workflow

1. Freeze and version the reviewed canonical JSON dataset.
2. Export the requested formats into a restricted output directory.
3. Open every generated file in its intended viewer.
4. Check fonts, layout, tables, special characters, links, page breaks and classification labels.
5. Compare finding counts and identifiers across all outputs.
6. Search outputs and metadata for secrets and unredacted evidence.
7. Record tool versions and export date.
8. Deliver only through the approved channel and retain/delete files per engagement rules.

## Limitations

- PDF and DOCX require an external Pandoc installation; PDF additionally requires a supported PDF engine.
- The initial HTML output is a self-contained readable report, not a customizable branding system.
- SARIF omits a source location when none is supplied; it is not a universal representation of every penetration-test observation.
- CSV contains one row per finding and is not a replacement for the narrative report.
- No export format automatically redacts all secrets, personal data or embedded file metadata.

## Output

The selected export files, the canonical JSON source, tool/version notes and a completed visual and content review checklist.
