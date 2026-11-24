# template.yaml

A template directory contains `template.yaml` and background files (`bg/*.pdf|png|jpg`).
`new_deck.py --template DIR` copies it into the deck as `template/`.

```yaml
name: my-lab
page_size: {width_bp: 960, height_bp: 540}      # 16:9; 4:3 would be 720 x 540
backgrounds:                                    # PDF (vector, preferred), PNG or JPG
  cover: bg/cover.pdf
  agenda: bg/agenda.pdf                         # optional
  section: null                                 # optional; null -> body background
  body: bg/body.pdf                             # required for a non-white deck
  closing: bg/closing.pdf                       # optional; importer falls back to cover
safe_area: {x: 56, y: 96, w: 848, h: 392}       # free content region of the body page
title: {x: 56, y: 30, w: 848, h: 52}            # frame title box (text vertically centred)
page_number: {x: 844, y: 508, w: 60, h: 20}     # right-aligned frame number
palette:                                        # names become LaTeX colours lsPrimary, ...
  primary: "#123A5A"     # headings, emphasis
  accent: "#3F86D8"      # diagrams, markers
  muted: "#4F5964"       # captions, details
  text: "#111111"
  title: "#111111"
  rule: "#BFCAD5"        # thin lines
  inverse: "#FFFFFF"     # text on dark areas
fonts:                                          # first installed font wins
  latin: {heading: [Arial, TeX Gyre Heros], body: [Times New Roman, TeX Gyre Termes]}
  cjk:   {heading: [Microsoft YaHei, Noto Sans CJK SC], body: [SimSun, Noto Serif CJK SC]}   # Simplified Chinese
  cjk_ja: {heading: [Yu Gothic, Noto Sans CJK JP], body: [Yu Mincho, Noto Serif CJK JP]}  # optional; built-in
  # defaults exist for ja, ko and Traditional Chinese (cjk_ja / cjk_ko / cjk_hant override them)
  math: [TeX Gyre Termes Math, Latin Modern Math]   # unicode-math font for equations and chart labels
sizes: {title: 24, heading: 19, body: 17, detail: 15, caption: 12, number: 12}   # pt
```

Coordinates are bp from the top-left corner. All boxes must be integers.

## Built-in templates

| Name | Look |
|---|---|
| `paper` | warm paper ground, deep indigo structure, terracotta accents; serif headings (Charter / Songti), sans body (Arial / PingFang) |
| `default` | white, navy and blue; Arial headings, Times / SimSun body |

Use them by name: `new_deck.py --template paper`, or `template: paper` in `deck.yaml`.
Backgrounds are drawn in TikZ (`templates/<name>/src/backgrounds.tex`); rebuild them with
`scripts/dev/build_template.sh <name>`.

## Importing

| Source | Command | Notes |
|---|---|---|
| PowerPoint template | `import_template.py OUT --pptx t.pptx` | Renders every layout (empty placeholders are invisible). Reads theme colours, theme fonts, and title/body/slide-number placeholder boxes of the body layout. Check `layouts.png`, then fix roles with `--map body=4,cover=1,... --force`. |
| PowerPoint slides | `--pptx t.pptx --mode slides --map cover=1,body=2` | For templates whose design lives on slides, not layouts. Run without `--map` first to get `slides.png`. Pick slides without sample text; slides that are one full-page picture are reported (their text is baked in). |
| One PDF | `--pdf bg.pdf --map cover=1,body=2,closing=3` | Page numbers, 1-based. |
| Separate files | `--cover a.pdf --body b.png --closing c.jpg` | PDF page 1 or images. |

Override boxes on the command line with `--safe x,y,w,h`, `--title x,y,w,h`,
`--number x,y,w,h`, or edit `template.yaml` afterwards.

## Fitting a template (what the agent must do)

1. Open `preview.png`. The **green** safe area must not overlap logos, bands or
   decorations of the body background; the **blue** title box must sit where the
   template expects the title; the **red** box where the page number goes.
2. Adjust the boxes, re-run the import with overrides (or edit YAML and render one test
   deck), and compare again.
3. Pick palette colours from the template. PPTX theme colours are imported; for PDF and
   images the importer samples `primary` (dark) and `accent` (saturated) from the
   backgrounds and prints them. It also warns when the title box or safe area covers
   background decoration.
4. Set fonts the template uses, followed by free fallbacks that exist on most systems.
5. Show the preview and one rendered body slide to the user for approval.

Templates usually carry copyright or institutional branding: keep user templates out
of public repositories unless the user says otherwise.
