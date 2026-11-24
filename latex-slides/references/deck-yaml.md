# deck.yaml

The single source for everything that is not slide content.

```yaml
title: GRPO in Practice
presenter: Ada Lovelace
date: '2026-11-20'
language: en                 # BCP-47; zh*/ja*/ko* load xeCJK and CJK fonts, and CJK note labels
duration_seconds: 900        # check.py compares with the sum of slide seconds
sourcing: normal             # strict: every content slide must list sources (FAIL)
                             # normal: missing sources -> WARN;  off: not checked
output_name: GRPO_in_Practice_2026-11-20    # output/<name>[_vN].pptx|pdf|notes.md
template: template           # template directory relative to the deck, or a built-in name (paper, default)
build:
  svg: true                  # false = PNG-only slides (same as --raster-only)
  png_width: 1920            # PNG fallback resolution (px)
checks:
  max_text_units: 150        # CJK chars + 1.6 x English words of body text per content slide
  min_text_units: 25         # below this (lead/takeaway excluded) and with < 2 visuals: density WARN
  duration_tolerance: 0.10
  render_diff_threshold: 12  # mean abs pixel diff (0-255) PDF vs LibreOffice-rendered PPTX
  max_svg_mb: 5
slides:
  - id: grpo                 # matches \slideid{grpo} in deck.tex (order follows deck.tex)
    title: Group-relative advantages
    seconds: 60
    script: |
      Spoken text for the presenter. End with a bridge to the next slide.
    sources:
      - "Shao et al., DeepSeekMath, arXiv:2402.03300, Sec. 4.1, Fig. 4"
    text_only_reason: null   # set to a sentence to allow a slide without visuals
```

Build errors if a frame has no `\slideid`, an id is missing from `deck.yaml`, an id is
unused, or two frames share an id.

## Generated from deck.yaml
- PPTX speaker notes (title, suggested time, script, sources)
- `output/<name>.notes.md` (printable speaker script)

## check.py

| Check | Level |
|---|---|
| missing glyphs, overfull boxes > 2pt, undefined references | FAIL |
| PDF pages = PPTX slides; durations within tolerance | FAIL |
| text outside safe area / title / page-number boxes | FAIL |
| overlapping text (e.g. a wrapped caption running into the takeaway) | FAIL |
| content slide without visual (no `text_only_reason`) | FAIL |
| body text over budget | FAIL |
| content slide with almost no explanation and a single visual | WARN |
| SVG contains live `<text>` | FAIL |
| PPTX integrity: svgBlip present, notes present, relationships resolve, videos | FAIL |
| hyperlinks preserved | FAIL |
| PPTX rendered by LibreOffice differs from PDF | FAIL |
| missing scripts, missing sources (normal), SVG masks, large SVG, minor overfull | WARN |
| PPTX-vs-PDF render skipped (LibreOffice missing or failed) | WARN |

The report goes to `verify/<version>.json`; record the visual review in its
`visual_review` field.
