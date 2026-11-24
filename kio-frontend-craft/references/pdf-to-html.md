
# 📚 Kio Master PDF to HTML Powerhouse — Execution Guide for AI Agents

This document is the **mandatory knowledge base** for an AI agent handling any request to **convert a PDF to HTML or build a website from a PDF design document**.

---

## 🚀 THE 4-STEP PROCESS THAT STOPS "AI-INVENTED UI/UX" WHEN A PDF EXISTS

When the user provides a design PDF (brochure, presentation deck, landing page design), the AI **MUST NEVER** read the raw text and imagine a web interface. Follow these 4 steps in order:

```
[STEP 1: CONVERT THE PDF TO A STANDALONE HTML FILE]
   bash convert.sh <input.pdf> <output.html> --fmt jpg
                    │
                    ▼
[STEP 2: EXTRACT ASSETS & DESIGN SPEC]
   python3 extract_sections.py <output.html>
   ➔ Creates <output>_extracted/
      ├── images/ (section_01_bg.jpg ... section_12_bg.jpg)
      └── design_spec.md (list of sections, text, CTAs, exact palette)
                    │
                    ▼
[STEP 3: BUILD A 1:1 RESPONSIVE WEBSITE FROM THE SPEC]
   • NO taste skills / brainstorming to invent a new UI.
   • Declare exactly the 4-5 hex colours from design_spec.md in _tokens.scss / theme.json.
   • Build one section per slide, using the original backgrounds and 100% of the original text.
                    │
                    ▼
[STEP 4: VISUAL QA (SIDE-BY-SIDE DIFFING)]
   • Playwright screenshots the real build.
   • Compare 1:1 against the original images/section_XX_bg.jpg.
   • Any difference ➔ REJECT immediately and fix until it matches.
```

---

## 🛠️ BUNDLED TOOLS

### 1. Conversion script: `convert.sh`
Located at `scripts/convert.sh`:
```bash
# Convert a PDF to HTML with every image embedded as Base64
bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --fmt jpg
```

### 2. Asset extraction script: `extract_sections.py`
Located at `scripts/extract_sections.py`:
```bash
# Extract clean background images and map the design spec to Markdown
python3 "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/extract_sections.py" output.html
```

---

## 🧠 1. Core Principles for the AI Agent

When asked to *"convert a PDF to HTML"*, the agent **MUST NOT** guess or run commands blindly. Three invariant principles apply:
1. **"One standalone file" (self-contained HTML)**: by default, always produce **a single HTML file** with all CSS, fonts and images embedded as Base64 data URLs. The user can email it and open it in any browser without missing images or broken layout.
2. **"Preserve text encoding & coordinates"**: never lose Unicode characters (for example Vietnamese diacritics, UTF-8) and keep every pixel of text, tables and vector graphics in place.
3. **"Inspect first, execute second"**: always run a quick inspection of the PDF (page count, encryption, text vs scan) before choosing an engine.

---

## 🗺️ 2. AI Decision Matrix

Before converting, run this quick check:

```
                  ┌───────────────────────────────┐
                  │ Inspect the input PDF (SOP-1) │
                  └──────────────┬────────────────┘
                                 │
                   Is it a scanned PDF (images only)?
                    ┌────────────┴────────────┐
                   YES                        NO
                    │                         │
     [Warn the user that OCR is               │
      needed first, or use raster             │
      image extraction mode]                  │
                                              ▼
                                 What does the user want?
                    ┌─────────────────────────┴─────────────────────────┐
                    ▼                                                   ▼
       [100% pixel-perfect layout]                           [Semantic / blog / web]
  - Financial reports, magazines, brochures              - Wants copyable text, mobile responsive
  - Keep the print layout, absolute positions            - Semantic <h1>, <p>, <table> tags
                    │                                                   │
     Use the skill script: convert.sh                        Use Python fitz / docling
     (Poppler or pdf2htmlEX engine)
```

---

## 🧭 3. The Agent's 4-Step Standard Procedure (SOP)

When the user provides a PDF to convert, the agent performs these 4 steps in order:

### 📍 Step 1: Pre-flight Inspection
Run these 2 commands to understand the document:
```bash
# 1. Page count, page size and password protection
pdfinfo "/path/to/document.pdf"

# 2. Does the file have a text layer, or is it a scan?
pdftotext "/path/to/document.pdf" - | head -n 30
```
- **If `Encrypted: yes`**: tell the user the file is password protected and ask for the password (`-upw <password>`).
- **If the text is empty (`pdftotext` prints nothing)**: it is a scan. Warn the user the result will be embedded images and text cannot be selected unless OCR is run.
- **If `Pages > 50`**: consider asking whether to split pages (`--split-pages`) so the browser does not lag on load.

---

### 📍 Step 2: Choose the Engine & Options

The standard script is at:
`${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh`

Choose the engine from this table:

| Situation | Recommended engine | Command |
| :--- | :--- | :--- |
| **Default / small output (<10MB)** | `poppler` with `--fmt jpg` | `bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --fmt jpg` |
| **Lossless vector art / crisp text (PNG)** | `poppler` with `--fmt png` | `bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --fmt png` |
| **Art magazines, premium custom fonts** | `pdf2htmlex` (Docker) | `bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --engine pdf2htmlex` |
| **Long documents (>30 pages) for quick browsing** | `poppler` with `--split-pages` | `bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --split-pages` |
| **Small page format, needs larger text** | `poppler` with `--zoom 1.5` | `bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" input.pdf output.html --zoom 1.5` |

---

### 📍 Step 3: Run the Conversion
Run the bundled wrapper:
```bash
bash "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh" "/path/to/file.pdf" "/path/to/file.html"
```
*Note*: the script already passes `-c` (complex layout), `-s` (single page), `-noframes` and `-dataurls` (embed everything as Base64).

---

### 📍 Step 4: Quality Verification Gate
After the run, the agent **MUST** check the following before replying to the user:
1. **File exists and has a sensible size**:
   ```bash
   ls -lh "/path/to/file.html"
   ```
   *Size must be above 0 bytes (usually tens of KB to a few MB depending on images).*
2. **Content integrity**:
   ```bash
   grep -i "<p" "/path/to/file.html" | head -n 5
   ```
   *Make sure paragraph tags and text are present.*
3. **Character encoding**: make sure accented characters have not turned into `?`, `` or other garbage.

---

## 📱 4. Viewer Optimisation (Responsive & Viewer Wrapper)

Pixel-perfect HTML uses fixed coordinates (`position: absolute`), so on phones or narrow windows it can overflow horizontally.

The agent can suggest or help wrap the HTML in a professional viewer container:
```html
<!-- Centre every document page in a viewer frame -->
<style>
  body {
    margin: 0;
    padding: 20px 0;
    background-color: #525659; /* Standard Acrobat/Chrome reader background */
    display: flex;
    flex-direction: column;
    align-items: center;
    overflow-x: auto;
  }
  /* Keep a shadow on every document page */
  .pdf-page, div[id^="page"] {
    box-shadow: 0 4px 15px rgba(0, 0, 0, 0.35);
    margin-bottom: 25px;
    background: #ffffff;
  }
</style>
```

---

## 🚨 5. Troubleshooting & Edge Cases

| Symptom | Cause | What the agent does |
| :--- | :--- | :--- |
| **`Permission denied`** | Script is not executable | Run: `chmod +x "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/convert.sh"` |
| **`Docker daemon not running` or network timeout** | The `pdf2htmlex` docker image cannot be pulled | Switch to `--engine poppler` to use the native tool with no network |
| **Blank HTML with no text** | The PDF is a scan or pure graphics | Use `pdftoppm -png -r 150 file.pdf page` to extract high-quality images, or run OCR |
| **PDF asks for a password** | Password-protected file | Add the password option: `-upw <password>` |
| **Overlapping characters** | The source PDF uses non-standard kerning | Add `--zoom 1.5` to scale coordinates so text spaces out naturally |

---

## 📋 6. Standard Reply Template for the User

When done, report briefly and professionally:

```markdown
### 🎉 PDF converted to HTML (pixel-perfect)

- 📄 **Source file**: `[PDF path]`
- 🌐 **HTML output**: `[HTML path]`
- ⚙️ **Engine**: Poppler pdftohtml / pdf2htmlEX (standalone single-file HTML)
- 📊 **File size**: `[size, e.g. 250 KB]`
- 💎 **Result**:
  - 100% of the original layout, vector graphics and fonts preserved.
  - All CSS, fonts and images embedded as Base64 data URLs (one file, opens offline anywhere).
  - Unicode text rendered correctly.
```
