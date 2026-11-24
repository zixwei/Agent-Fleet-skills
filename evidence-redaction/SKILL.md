---
name: evidence-redaction
description: Review and sanitize screenshots, request/response excerpts, logs, documents and report datasets before security evidence is stored or shared.
---

> 边界:仅限授权测试。只用于已获书面授权的渗透测试目标。

# Evidence redaction

## Purpose

Use this skill before including assessment evidence in a report, ticket, presentation, archive or email. Redaction must prevent recovery of secrets and personal data, not merely make them visually harder to notice.

## Prerequisites

- The engagement's evidence-handling and retention requirements are known.
- Evidence is stored in a restricted working directory.
- The intended recipients and permitted disclosure level are clear.
- The operator retains an unmodified original only where the engagement permits it and protects it separately.

## Threats to consider

Evidence may expose:

- Passwords, API keys, bearer tokens, session cookies, private keys and recovery codes.
- Personal, financial, medical or customer-specific records.
- Internal IP addresses, hostnames, usernames, email addresses or tenant identifiers.
- Request bodies, response headers, browser URL bars, QR codes, barcodes and hidden form fields.
- EXIF, document author, timestamps, GPS coordinates, revision history and embedded attachments.
- Secrets repeated in filenames, image metadata, PDF text layers, hyperlinks or OCR/searchable layers.

## Tools

- **ExifTool:** inspect file and image metadata — [documentation](https://exiftool.org/).
- **MAT2:** remove supported metadata from files — [project documentation](https://0xacab.org/jvoisin/mat2).
- **GIMP:** manually obscure visual evidence in a flattened copy — [documentation](https://docs.gimp.org/).
- **Adobe Acrobat Pro** or another approved PDF redaction tool: apply irreversible PDF redaction and sanitize hidden information. Follow the vendor's current instructions.

Metadata removal does not redact content. Blur, highlight, drawing or placing a black rectangle over text may leave the underlying pixels or text recoverable. Use a tool's actual redaction operation or create a flattened, reviewed derivative.

## Workflow

### 1. Minimize collection

Capture only the evidence necessary to demonstrate the issue. Prefer a short excerpt, a synthetic record and a minimal screenshot over full proxy archives or database exports.

### 2. Classify sensitive content

Review every visible and hidden layer, including request/response headers, cookies, query parameters, file names, URL bars, image metadata and document properties. Treat unexpected production data as a stop condition and follow the incident procedure.

### 3. Create a redacted derivative

Keep the original restricted only if retention is permitted. Apply irreversible redaction to a derivative. Replace secrets with descriptive labels such as `[REDACTED TOKEN]`; do not use realistic fake secrets that could be confused with live credentials.

For images, crop unnecessary areas and flatten a redacted copy. For PDFs, apply the PDF application's redaction function, then sanitize hidden information. For metadata, inspect first and remove metadata only when the engagement permits it.

### 4. Verify redaction

- Reopen the exported file in a different viewer.
- Select, search, copy and paste text near each redaction.
- Inspect document properties and image metadata.
- Check layers, attachments, links, comments and OCR/searchable text where relevant.
- Confirm blacked-out regions cannot be removed or undone.
- Run a second-person review for high-risk deliverables.

Do not rely on automatic secret scanners as proof that all personal or confidential content has been removed.

### 5. Record the evidence trail

Maintain a secure evidence index with evidence ID, original filename, derivative filename, collection timestamp, redaction operator, redaction method and reviewer. Avoid including sensitive values in the index itself.

## Stop conditions

Stop and notify the engagement owner if real customer records, credentials, private keys or regulated data were collected unexpectedly. Do not test discovered credentials, expand collection or share raw evidence to explain the issue.

## Output

A minimized and reviewed redacted evidence set, a secure evidence index, and documented redaction limitations.

## Related skills

- [Report authoring](../report-authoring/SKILL.md)
- [Report quality assurance](../report-quality-assurance/SKILL.md)
- [Multi-format report export](../multi-format-report-export/SKILL.md)
