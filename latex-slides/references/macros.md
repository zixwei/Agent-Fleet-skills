# Layout macros (`latex-slides.sty`)

Coordinates are **bp from the top-left corner** of the page (default page 960 × 540 bp).
Numbers below refer to the default template; use the values of your `template.yaml`.

## Page roles

Body frames need nothing special. Other roles switch background and hide the page number:

```latex
{\lsrole{cover}
\begin{frame}[plain]\slideid{cover}
  ...
\end{frame}}
```

Roles: `cover`, `agenda`, `section`, `body`, `closing`. Missing backgrounds fall back to `body`.

Every frame needs `\slideid{<id>}` matching an entry in `deck.yaml`.

## Template values available in LaTeX

| Macro | Meaning |
|---|---|
| `\lsSafeX \lsSafeY \lsSafeW \lsSafeH` | content box (bp) |
| `\lsContentTop` `\lsContentBottom` | band between `\lead` and `\takeaway` |
| `\lsPageW \lsPageH` | page size |
| `\lsSizeBody`, `\lsSizeDetail`, `\lsSizeCaption`, `\lsSizeHeading`, `\lsSizeTitle` | font sizes (pt) |
| colours `lsPrimary lsAccent lsMuted lsText lsTitle lsRule lsInverse` | palette |
| `\headingfont` | heading font family (Latin + CJK) |

## Placement

| Macro | Use |
|---|---|
| `\at{x}{y}{w}{content}` | text block with top-left corner at (x, y) and width w |
| `\pic{x}{y}{w}{h}{file}` | image fitted into w × h (counts as a visual) |
| `\figat{x}{y}{w}{h}{file}{caption}` | image plus caption below |
| `\captionat{x}{y}{w}{text}` | caption-sized muted text |
| `\splitpic[ratio]{file}{caption}{text}` | picture left, text right, between lead and takeaway; ratio = picture share (default 0.55) |
| `\textpic[ratio]{file}{caption}{text}` | text left, picture right |
| `\lsvideo{x}{y}{w}{poster}{media/file.mp4}` | poster in PDF (click opens file), embedded movie in PPTX |

## Dense layouts

For slides that carry a visual *and* its evidence: a row of figures, a grid of cards, headline numbers.

```latex
\begin{lsgrid}[top]{n}      % n cells per row across the safe area; top defaults to \lsContentTop
  \card{title}{text}                 % tinted card with a heading
  \cardpic[h]{file}{title}{text}     % picture (height h bp, default 110) above heading + text
  \fig[h]{file}{caption}             % framed picture (default 150 bp) + caption
\end{lsgrid}
```

Cells flow left to right and wrap after `n`; mix cell types freely. Pictures in `\cardpic`
and `\fig` count as visuals. Use two grids at different `top` values for a figure row
above a card row.

| Macro | Use |
|---|---|
| `\stat{value}{label}` | a big headline number with a small label (inside `\at`, a card or a cell) |
| `\callout{text}` | emphasised sentence with an accent bar |

```latex
\begin{lsgrid}[150]{3}
\fig[120]{baseline.png}{Baseline}  \fig[120]{ours.png}{Ours}  \fig[120]{diff.png}{Difference}
\end{lsgrid}
\begin{lsgrid}[310]{3}
\card{\stat{+3.5 pt}{AUROC}}{Mean over 15 classes.}
\card{\stat{2.3×}{faster}}{41 s vs. 95 s per epoch.}
\card{Caveat}{Seed 0 only; three seeds are running.}
\end{lsgrid}
```

## Text styles

| Macro | Use |
|---|---|
| `\lead{...}` | one-sentence question/claim at the top of the safe area |
| `\takeaway{...}` | rule + conclusion at the bottom of the safe area |
| `\heading{...}` | small coloured heading |
| `\point{heading}{text}` | heading + paragraph |
| `\detail{...}` | smaller muted text |
| `\key{...}` | emphasis in primary colour |
| `\lsChapter{01}` | prefix later frame titles with "01." |

## Visual detection

`check.py` counts visuals per page. Every `\includegraphics` (and so `\pic`, `\figat`,
`\splitpic`, `\textpic`, `\fig`, `\cardpic`), `\lsvideo`, `tikzpicture` (including pgfplots),
`tabular` and `tabularx` count automatically; template backgrounds do not. For anything
else (e.g. a picture drawn with `\pgfimage`), add `\lsMarkVisual` inside the frame.

## Typical body slide

```latex
\begin{frame}{Group-relative advantages remove the value model}\slideid{grpo}
\lead{How does GRPO score a response without a critic?}
\splitpic[0.6]{grpo.png}{Figure: Shao et al. 2024, Fig. 4.}{%
  \point{Sample}{$G$ answers per prompt.}\vspace{10pt}
  \point{Normalise}{Reward minus group mean, divided by std.}}
\takeaway{The learning signal comes from differences inside the group.}
\end{frame}
```

## Tips

- Plain Beamer content (itemize, columns) also works; it flows from the top of the safe area.
- Keep at least ~8 bp between blocks. check.py flags text outside the safe area and
  overlapping text, but not text drawn over pictures, so look at the previews.
- Use `\onslide<2->{...}` for build-ups; each step becomes its own slide.
- File names used by `\lsvideo` must not contain spaces.
