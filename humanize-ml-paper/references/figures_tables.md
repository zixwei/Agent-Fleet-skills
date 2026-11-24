# Figures and tables: palette, plot style, diagrams, captions, table conventions

Hex values were pixel-sampled from the source figures. Evidence labels (`[all]`, `[most]`, `[some]`, `[venue: X]`, `[current]`, `[older]`, `[fix]`, `[skill choice]`) are defined in `SKILL.md`. Code: `templates/plot_style.py`, `templates/housediagram.sty` with `scripts/render_diagram.py`, `templates/tables.tex`, `templates/preamble.tex`. Reference figures on invented data: `assets/` (§5), rendered by `assets/make_assets.py`.

## 1. The look in one paragraph

Data plots are matplotlib PNGs, one PNG per panel, assembled in LaTeX with `subfigure`. Everything is bold Times, large in the source so it stays legible at 0.3-0.5 text width. Heavy four-sided box; light dashed y-only grid `[current]`. Two colour families (blue, pink): the saturated shade is "ours / with", the pale tint of the same hue is "baseline / without". Concept and framework diagrams (drawn in PowerPoint/Keynote in the house papers, in TikZ on `templates/housediagram.sty` in this skill, §5): a white-dominant canvas of white rounded boxes whose colour lives in saturated 1.1-pt outlines (rose = trainable, navy = fixed; amber only for score and feedback arrows), one filled focal module, bold sans labels with LaTeX math, a compact legend, and a concrete example inside.

## 2. Files and tooling

- One matplotlib PNG per panel; multi-panel figures composed in LaTeX, never `plt.subplots(1, n)` `[all]`. A sweep over several settings and model sizes becomes many separate files (one per panel), composed with `subfigure` in one or more `figure*`.
- 600 dpi PNG `[current]` (300 dpi `[older]`). `plot_style.save()` also writes a PDF; ICML asks for vector plots where possible, so include the PDF there (`venues.md` §2.2).
- Diagrams: the house figures came from a slide tool as a PNG of at least 300 dpi at the printed width `[most]` or a vector PDF `[some]`, with a Keynote look (drop shadows, soft rounded boxes) `[current]`; fills that are exact PowerPoint Office-theme swatches (`#FBE5D6`, `#843C0C`, `#203864`, `#4472C4`, `#F2F2F2`, `#7F7F7F`) are `[older]` (the peach/brown pair is not used in new figures, §5.1). This skill builds diagrams from TikZ sources with `scripts/render_diagram.py`: a vector PDF at the printed width, included unscaled, plus a PNG preview `[skill choice]` (§5).
- Files named by content in topic folders: `figures/exp/cost_quality/set_a.png`, `figures/exp/dynamics/val_acc.png`, `figures/framework/`, `figures/method/` `[current]`. Superseded versions are kept, never deleted `[most]`; the marker varies: a `*_old` suffix or folder, a `(drop)` suffix, `_v2/_v3` `[some each]`. Drafts point at `figures/Placeholder.png` `[most]`; paths from the project root with the extension, no `\graphicspath` `[all]` (`latex_source.md` §4).
- No titles on plots `[all]`; the subcaption is the title.

## 3. Palette (`[current]` look; the same values recur across the most recent figures)

| Role | Hex | Evidence |
|---|---|---|
| Blue, saturated: ours / main series | `#649CD8` | `[current]` |
| Blue tint: baseline / w/o | `#BFD8EF` | `[current]` |
| Blue text (axis label/ticks of the blue series) | `#527FD4` | `[some]` |
| Pink, saturated: ours in the 2nd metric family | `#FF85A1` | `[current]` |
| Pink tint: baseline / w/o | `#FFDAE2` | `[some]` |
| Pink text | `#F1667E` | `[some]` |
| Neutral baseline curve | `#A5A5A5` | `[some]` |
| Spines, ticks, legend edge | `#595959` | `[some]` |
| Grid (dashed, y only) | `#D3D3D3` / variant `#CFCFCF` | `[current]` |
| "Avg" separator (dashed vertical) | `#696969` / blue variant `#5799C6` | `[current]` |
| Hatch edge on dark-edged bars | `#464646` | `[some]` |
| Ordered sweep of one hyperparameter | `#DCD4BE` → `#BAAE8B` → `#8C8064` (chosen default darkest) | `[some]` |
| Categorical without hue | `#D6D6D6` `//`, `#595959` `xx`, `#999999` `\\` | `[some]` |

Rules:
- Saturated = ours/with, tint = baseline/without `[all]`.
- Ours moved from warm red (`#D64F38`, `#E56F5E`) `[older]` to blue `[current]`. Use blue.
- Option `[some]`: when a figure has two metric families, the metric picks the hue (performance = pink, cost = blue), and inside each family saturated = ours (`assets/plots/cost_quality_dual_axis.png`).
- One colour, one meaning across diagram, plot and LaTeX text `[most]`: e.g. accent A is rose `#DB687B` in every figure and `\definecolor{roleA}{RGB}{220,105,123}` in the text; the outline `#515B83` of a region in the diagram is also `goodboxborder` of the matching case-study box; a state box keeps its colour in every diagram; frozen modules carry a navy outline and trainable ones a rose outline throughout.
- LaTeX mirrors of the palette: `plotblue`, `plotbluelight`, `plotpink`, `plotpinklight`, `plotgray`, `roleA`, `roleB` in `templates/preamble.tex`.
- The hex values are the house default. To use your own hues, call `plot_style.set_palette(blue=..., blue_light=..., ...)` before `apply()` and change the matching `\definecolor` lines; keep the roles (saturated = ours, tint = baseline) `[skill choice]`.

Diagram palette (framework/concept figures; the `hd...` colours of `templates/housediagram.sty` and `plot_style.DIAGRAM` hold the same values). Colour lives in the outlines; the rules that make it look clean are in §5.1.

| Element | Hex | Evidence |
|---|---|---|
| Canvas, box and chip interiors | white `#FFFFFF` (about 80 % of the pixels) | `[current]` |
| Primary outline hue A: rose (trainable modules / branch A) | `#DB687B` | `[current]` |
| Primary outline hue B: navy (frozen or fixed modules / branch B) | `#143973` | `[current]` |
| The one accent: amber, only for dashed score / loss / feedback arrows | `#FFB84D` (1-pt strokes `#F5A83C`) | `[current]` |
| Semantic correct / incorrect (fills or marks, at most these two states) | teal `#67AB9F` / coral `#EA6B66` | `[some]` |
| Text: bold titles, italic annotations, math | ink `#1F1F1F` | `[current]` |
| Neutral outlines, frames, item chips | `#595959` | `[some]` |
| Group panel: very light, white dashed edge, soft shadow | `#F5F5F5`-`#F7F7F7` (house `#F6F6F6`) | `[current]` |
| Input box: dark mauve-gray square with a white calligraphic symbol (signature) | `#594E54` | `[current]` |
| One highlighted object / state, if a figure needs it | pale navy `#E3EAF5` under a navy outline | `[skill choice]` |
| The one filled focal module (§5 rule 2) | navy fill `#214685` (`hdnavyhero`) or rose `#DB687B`, white bold text, white 1.2-pt rim | `[skill choice]` |

Older source figures fill trainable modules peach with a brown border (`#FBE5D6`+`#843C0C`) and items khaki (`#F2EDDF`+`#BAAF8B`) `[older]`. Next to the saturated outlines those earthy fills read as muddy; do not use them as the default module or item style `[skill choice]`.

## 4. Plot typography, axes, marks (`plot_style.apply()`)

- Bold Times New Roman for ticks, axis labels, legend `[most]`. Bar charts that came out in DejaVu Serif were a silent font fallback, an accident `[fix]`; `apply()` warns.
- Sizes: ticks 16 pt, labels 18 pt, legend 14 pt; choose the figure size so printed ticks are ~7 pt: `font_pt = 7 × figure_width_in / printed_width_in` `[current]` (`[older]` plots printed 4-5.5 pt, too small). 7.7 in wide → `\columnwidth` (ICML, 3.25 in) or `0.59\textwidth` in one column; 3.75 in square → `0.49\columnwidth` or `0.29\textwidth` (this corrects an earlier 0.45/0.25, which prints 5 pt ticks `[fix]`). Keep a single-column ICML plot's source at 7.5-8 in: a 9.7-in source prints 5.3 pt ticks.
- Axis labels: unit in parentheses `[all]`. Case varies, so be consistent within a paper: Title Case ("Coverage Score (%)", "Mean Rank") `[some]`; sentence case ("Filtering time (min)", "Training set size (k)") `[some]`; one-word labels "Step", "Accuracy (%)" fit both `[current]`. Metrics as percentages.
- Box: four spines `[all]`. Current look: dashed y-only grid behind data `[current]`; a grid on both axes, or none, `[older]`. 2.2 pt `#595959` spines with matching `#595959` inward major ticks and black tick labels `[some]` (black spines and ticks also `[some]`; `[older]` look 3.5 pt black, `apply(era="older")`).
- Legend inside the axes in the emptiest corner with bold entries `[all]`: thin (1.5 pt) rounded `#595959` frame `[most]`, or unframed (`frameon=False`) on compact square dynamics panels `[some]`; ≥3 bar series → legend above the axes (`y = 1.02`) with `ncol` 2-3 `[some]`.
- Twin y-axes coloured to their series with the darker text shade; bars on one axis, line with markers on the other, with proportional limits so both y-axes' major ticks align on the grid `[some]`.
- Lines ~2.6 pt, markers `o`/`s` ~9 pt `[current]`. Noisy training curves: thin translucent raw curve (0.9 pt, α≈0.16) under a thick trailing rolling mean of the same colour that starts at `x[0]` (no leading gap `[fix]`), and the caption/text says so `[current]`.
- Bars: dense hatches (`///`, 1.0 pt) instead of extra hues: white on saturated fills `[most]`, a dark `#464646` hatch + 1.0 pt edge on variants `[some]` and always on pale tints (`#FFDAE2`, `#BFD8EF`), where a white hatch vanishes `[fix]`; bold 2-decimal value labels; an "Avg." group at the right behind a dashed separator `[current]`.
- Reference lines: dashed gray for events/thresholds ("Best baseline") `[some]`.
- No error bars `[current]`; seed bands `[older]`.

Plot-type catalogue: headline gain on p1 → dynamics curve + final bar chart `[some]`; metric vs sweep → line with markers per model `[some]`; quality vs cost → dual axis, lines + bars `[some]`; trade-off frontier → one curve per method in the plane of the two competing objectives `[some]`; ablation → grouped bars + Avg `[current]`; training dynamics → square panel raw + smoothed `[current]`; representation geometry → PCA/t-SNE scatter with ellipses and marginal KDEs `[some]`; depth analysis → line against layer number `[some]`; matrix → annotated heat map, bold numbers `[some]`.

## 5. Framework and concept diagrams

The house figures were drawn in Keynote / PowerPoint `[current]`. This skill draws them in code: each diagram is a standalone TikZ source on `templates/housediagram.sty`, which carries the measured details of §5.2 as named styles, and `scripts/render_diagram.py` builds it into a vector PDF at the printed width plus a PNG preview `[skill choice]`. A slide tool is still fine for a human author: match §5.1-5.2 and export a PDF, or a PNG of at least 300 dpi, at the printed width.

**Workflow** (one draw, render, look and fix round takes about a second):
1. Copy `templates/diagram_skeleton.tex` to `figures/src/<name>.tex` (with a copy of `templates/housediagram.sty` next to it if the build cannot see the skill folder) and set `% target-width-in:` to the printed width of the float: ICML `\columnwidth` 3.25, `figure*` at 0.9\textwidth 6.10, `\textwidth` 6.75; ICLR / NeurIPS `\textwidth` 5.5 (0.9\textwidth 4.95), `wrapfigure{r}{0.48\textwidth}` 2.64.
2. Place every node relative to another (`right=12pt of`, `below=4.5pt of`, `(a |- b)`, `fit`, `\hdlane`), never at hand-computed coordinates, and give each node exactly one box style. `assets/diagram_src/style_sheet.png` shows every style with its name; the five sources in `assets/diagram_src/` are worked examples.
3. Build: `python3 <skill>/scripts/render_diagram.py figures/src/<name>.tex --out-dir figures/framework --check`. It stops on LaTeX errors, lists missing characters, overfull boxes and font substitutions, fails on Type 3 fonts or a width more than 3 % off the target, warns on raster images, and prints the clean-palette metrics of §5.1.
4. Open the PNG and look: no label on a line, no chip text wrapped to a second line, headers on one baseline, every arrowhead on its target's outline, nothing crowding a shadow. Fix a gap or a width; never scale the figure.
5. Include the PDF at the width it was drawn at: `\includegraphics[width=0.9\textwidth]{figures/framework/<name>.pdf}`.

**Rules:**
1. White rounded modules whose colour lives in a 1.1-pt outline: `trainable` rose, `frozen` navy (frozen or fixed), `neutral` gray; `state` (pale navy fill) for the one selected set `[current]`. LaTeX math labels that match the paper's symbols exactly `[all]` (the house figures pasted them via IguanaTeX / LaTeXiT; TikZ typesets them).
2. Exactly one filled focal object per figure `[skill choice]`: the key module as `hero` (navy, a fixed rule) or `hero rose` (trainable), with white bold text and math and a white 1.2-pt sticker rim; `hero gradient` (rose to navy) only for one module shared by two roles `[current]`; in a schematic, one filled bar can be the focal object. The dark input square must not be the strongest mark. (The house figures fill one model block `[current]`.)
3. Label face: bold grotesque sans (TeX Gyre Heros, a Helvetica clone) with Latin Modern math, as in the `[current]` house figures; `\usepackage[serif]{housediagram}` sets Times text and math instead. The earlier Times New Roman + STIX labels are retired `[fix]`. Three tiers: 9-pt bold headline, 7.5-pt bold titles and stage headers, 7-pt labels and italic notes and quotes; math at 1.08 ×; tags 5.6 pt; chip text is never hyphenated.
4. One colour, one meaning across every figure of the paper `[most]`: rose = trainable; navy = frozen or fixed, or ours in a comparison; amber = score, loss and feedback arrows only; teal / coral = kept-good / lost marks only; gray = neutral items, the existing method, single-stream data flow. Coloured text only in a headline that names the problem (coral) or the solution (navy), and in short failure notes (coral italic) `[some]`.
5. Items: quoted examples in `chip`s (`\hdquote{...}`; `tagged=n` when the reader follows an item across the figure), ids and scores in 15 × 15 `token`s; `chip selected` / `token selected` for what is kept, `chip dropped` / `token dropped` (square-dotted gray, muted text, no shadow) for what is skipped `[current]`.
6. Containers: edgeless `#F6F6F6` lanes (`\hdlane`) group items, never a single module; white shadowed cards (`\hdcard`) and perforated cards (`\hdperforated`) hold evidence panels; at most one 1.45-pt `#595959` heavy frame (`\hdframe`) per figure `[skill choice]` (frames inside frames read as a table); a dashed group (`\hdgroup`) marks a set, coral when the set is the failure (near-duplicates).
7. Connectors (§5.2.4): `data flow` (gray) for single-stream data, `role flow=<colour>` for one side's branch in a contrast figure, `score flow` (dashed amber) for scores, `feedback flow` (solid amber) for the update loop, `ghost flow` for inactive steps. Orthogonal with rounded elbows, left to right or clockwise; outputs leave east or south, never backwards out of an input face; no wire between a header and its lane `[skill choice]`.
8. No free-floating math: a formula sits in a module, in a rule lane, beside its arrow (`note`) or on it (`pill`). Worked numbers explain why an item is kept or skipped (p = .04 on the skipped copy) `[skill choice]`.
9. A concrete example inside `[most]`: real inputs in italic curly quotes (`\hdquote`), tied to their node by position, tag or callout tail; the same running example in every figure of the paper, at growing zoom (teaser, framework, mechanism) `[most]` (`prose.md` §1 rule 11).
10. Legend: one compact unframed row (`\matrix[legend row]`) of flat icons, at most 5 keys, under the diagram or in a top corner, never above the content; no key for what the labels already say (data flow, input), and no legend at all when 3 or fewer encodings are labelled in place `[skill choice]`. (The house figures use larger side legends `[current]`, §5.2.9.)
11. Problem vs solution on one canvas: two equal halves split by a dashed vertical gray line, each with a two-part headline (ink tag + coloured claim) and a verdict callout (`callout box` + `\hdtail`, teal tick or coral cross) under its output `[some]`; a problem teaser names the failure modes of the prior approach `[some]`.
12. Grey out the parts of a pipeline the caption does not discuss (`ghost`, `ghost flow`); a module that appears twice keeps its outline colour `[some]`.
13. Sizes: framework `figure*[t]` at 0.88-1.0\textwidth `[most]`; teaser 0.9\columnwidth or \columnwidth `[some]`; key-idea `wrapfigure{r}{0.48\textwidth}` `[venue: NeurIPS]` (one-column space saver; never in two columns). Draw at that printed width.

### 5.1 Clean palette: why the reference figures look clean, not muddy

Measured on the house reference figures `[current]`:

1. **White dominates (about 75-88 % of the pixels).** Modules and chips are white inside; colour lives in saturated outlines (1.1 pt at print, the same weight as the arrows; §5.2.1). Fills are reserved for the one focal object (§5 rule 2), the pale tint of the selected state (`#E3EAF5`), and at most two semantic states (kept-good = teal, lost = coral).
2. **Few hues.** Two role hues, rose `#DB687B` and navy `#143973` (`#214685` as a large fill), plus one accent, amber `#F5A83C`, only for score and feedback arrows. Teal `#67AB9F` and coral `#EA6B66` only for good / bad marks. Neutrals: ink `#1F1F1F` for text, `#595959` for frames, item outlines and data arrows, very light lanes, the dark `#594E54` input square with a white symbol.
3. **No earthy tones.** No peach fill with a brown border, no khaki fill with a khaki border, no brown titles or brick-red text. A very light cream may appear for one object type if a figure really needs it, never as the default item style.
4. **Black text.** Bold titles, italic notes and math are black (white on the filled focal object); gray text only for parts that deliberately recede (skipped items); colour only in a problem / solution headline and in short failure notes.
5. **Light, few panels; one shadow.** Lanes are very light (`#F6F6F6`), edgeless and few, and the items inside them stay white. One soft shadow (25 % black, §5.2.3) goes on every free-standing object, small chips included; objects inside a card, a frame or a legend stay flat (`no shadow`).
6. **One colour = one meaning** inside a figure and across the paper, and the legend names every colour it uses. Two reds that mean different things (rose = trainable, coral = lost) must not sit side by side without a distinct shape (a coral x badge) to tell them apart.

**Target metrics** for every diagram PNG (`render_diagram.py --check` prints them; `python3 scripts/colour_stats.py fig.png` checks any PNG):

| Metric | Target | Meaning |
|---|---|---|
| `white` | ≥ 0.72 | share of near-white pixels |
| `earthy/colored` | ≤ 0.05 | share of brown / khaki / beige / peach pixels among the coloured ones |
| `hue-families` | ≤ 3 (semantic teal / coral may add one) | hue bins that cover ≥ 6 % of the coloured pixels |
| `mean-sat` | 0.25-0.75 | mean saturation of the coloured pixels: below reads washed out, above reads loud. One filled focal module raises it: the reference diagrams measure 0.53-0.66, the house source figures 0.17-0.51 |

A failing `white` usually means tinted module fills or heavy panels; failing `earthy` means peach, khaki or brown; a low `mean-sat` means pale tints dominate and the outlines are too thin. Export a slide-tool diagram to PNG first.

**Reference figures.** Open these before drawing; they are the finish to aim for. All five show one invented method, X (score candidate prompts for difficulty and diversity, sample them with a fixed quota rule, train, and feed the loss change back to the scorers), on one running example: the prompt pool "2 + 3 = ?", "Add 2 and 3", "Sum 2 & 3" (near-duplicates), "Sort [3, 1, 2]", "Prove √2 ∉ ℚ" (a rare skill) and "Free deals!" (noise). Each is a standalone source `assets/diagram_src/<name>.tex`, built by `assets/make_assets.py` (through `render_diagram.py`) into `assets/diagrams/<name>.pdf` (vector, printed size) and `.png` (300 dpi). They show conventions, not results.

| File (float, printed size) | Convention to copy |
|---|---|
| `framework_pipeline` (framework `figure*` at 0.9\textwidth in ICML, 6.10 × 1.86 in) | four stages under headers on one baseline (Candidate Batch $\mathcal{B}_t$, Dual Scoring, Quota Sampling, Model Update); dark input square → a lane of four tagged, quoted chips → a fork labelled $x_i$ into two `trainable` scorers that fill their lane ($w_i = g_\phi(x_i)$, $q_i = h_\psi(x_i, \mathcal{B}_t)$) → dashed amber score arrows labelled $w_i$ and $q_i$ into the one filled focal module, the fixed `hero` "Quota Sampler" ($p_t(x_i) \propto w_i q_i$) → a selection lane whose tokens sit on the chips' rows, each with its p value; the skipped near-duplicate 2 is square-dotted with a coral .04 and an x badge → the `trainable` "Model" $f_\theta$; a solid amber feedback loop with the pill "$\Delta\ell$ updates $\phi, \psi$"; a centred legend row (Trainable, Fixed rule, Selected / skipped as a two-state stack, Score, Feedback) |
| `teaser_problem_solution` (intro teaser, `figure*` at \textwidth in ICML, 6.74 × 2.40 in) | two equal halves split by a dashed gray line, each with a two-part headline (ink tag; coral problem, navy solution) and the same pool lane of five quoted items with gray score bars; existing = a `neutral` Top-$k$ box, gray arrows and a coral dashed ring around the three kept copies; ours = the filled `hero` "Diverse Top-$k$" ($s(x) - \lambda r(x)$), navy arrows and coral hatched penalty segments on the two copies; skipped items square-dotted in both pools; each Kept lane bottom-aligned with its pool, so a short vertical `\hdtail` joins it to a verdict callout (coral x "3 of 3 slots spent on one easy question" / teal tick "3 distinct skills at the same budget"); a legend row |
| `teaser_failure_modes` (intro teaser, `\columnwidth`, 3.25 × 2.89 in) | headline "Prior pipeline: one frozen score" (the problem in coral); a lane of four tagged chips with score bars (teal outline = useful, gray = noise) → the filled navy "Scorer" $s(x)$ with a frozen snowflake badge → a navy top-$k$ branch into the kept tokens 1, 2 inside a coral "near-duplicates" ring, and a gray rest branch into the dotted tokens 3, 4 with an x on the rare proof ("rare skill lost"); two equal perforated evidence cards flush with the row above: (a) pool vs kept share per skill (gray / navy bars, "0 kept" on Proof), (b) distinct skills against the budget (a coral top-$k$ plateau under a dashed gray ideal); a four-key legend row |
| `key_idea_loop` (key-idea `wrapfigure{r}{0.48\textwidth}` `[venue: NeurIPS]`, 2.65 × 1.55 in) | the framework as one loop, no legend: input square → the filled `hero rose` "Scorers" ($g_\phi, h_\psi$) → sample → the `state` "Batch" $\mathcal{B}_t$ → train → the `trainable` "Model" $f_\theta$ → solid amber "update by $\Delta\ell$" back into the scorers; a dotted skip branch to the dropped chip "Add 2 and 3" ($p{=}.04$, x badge) |
| `cluster_quota` (method figure, `\columnwidth`, 3.25 × 2.75 in) | how the diversity scorer works: input square → `trainable` "Encoder" $h_\psi$ → cluster into two perforated cards whose lanes align row for row: item scores $w_{c,j}$ as flat tokens per group $C_1..C_4$ (the rare item of $C_4$ selected), and "Group quota $q_c \propto n_c^{-\alpha}$" as bars against a dotted uniform line (0.8×, 1.0×, 1.2×, and the one filled bar, 1.7×, for the rare group); a rule lane with $p_t(x_{c,j}) \propto w_{c,j} q_c$ and the worked "$C_4$: .8 × 1.7, sampled" with a teal tick; a two-key micro legend top right |
| `assets/plots/cost_quality_dual_axis.png` | dual axis: pink quality lines (tint = baseline, saturated = ours) over blue cost bars; axis labels and ticks coloured by series; `#595959` box, dashed y-grid shared by both axes; one-column legend in the empty band left of the bars |
| `assets/plots/dynamics_sweep.png` | square panel; khaki sweep `#DCD4BE` → `#8C8064` for one hyperparameter (default darkest); faint raw curves under thick smoothed ones that start at step 0; unframed legend in math, highest curve first |
| `assets/plots/grouped_bars_hatched.png` | grouped bars: tint = base, saturated = with the method; dark-edged hatched bars for a variant of it; bold 2-decimal value labels; "Avg." group behind a dashed blue separator; legend in three columns |
| `assets/plots/ablation_bars_pink.png` | ablation in the pink family: full method saturated, variants in the tint (the hatched one with a dark edge and hatch, since a white hatch vanishes on a pale tint); "Avg." behind a `#696969` dashed separator; ≥3 series → legend above the axes |

### 5.2 Diagram details (measured)

Measured by pixel on the house reference figures and converted to **printed pt** (1/72 in on paper), given as median [range]. The `[current]` figures set every value. They were drawn in two different tools but share one set of drawing rules (the same shadow, stroke, dash and cap-height sizes), so the look is one rule set, not a per-figure choice. `[older]` slide-tool figures had no shadows, fill-only modules, bold serif labels and 4:3 dashes; follow `[current]`. `templates/housediagram.sty` implements every value below (styles in the last column).

#### 5.2.1 Strokes

| Element | Spec | `housediagram.sty` |
|---|---|---|
| Object stroke: tokens, module boxes, op boxes, circles, chips | **1.1** [0.93-1.35]; modules and small items use the **same** weight `[current]` | `hd base`, `chip`, `token`, `operator` |
| Frame / panel box (heavy grey) | **1.45** [1.3-1.7], `#595959`, about 1.3 × the object stroke `[current]` | `heavy frame` (`\hdframe`) |
| Arrow shaft | **1.1**, always the same width as the box stroke `[current]` | `data flow`, `role flow`, `score flow`, `feedback flow` |
| Dashed group container | 1.1 [0.92-1.3] `[current]` | `dashed group` (`\hdgroup`) |
| Small operator badges | 0.5 [0.45-0.56] `[current]` | `badge` |
| White "sticker" outline on filled shapes | 1.2 [1.0-1.37] `[current]` | `hero`, `hero rose`, `hero gradient` |
| White dashed outline on a white panel ("perforated" edge) | 1.8, dashes 5 on / 5 off `[current]`, over a faint 0.8-pt hairline that keeps the top and left edges visible on a white page `[skill choice]` | `perforated card` (`\hdperforated`) |
| Plot axes drawn inside a schematic | 0.8-1.1 `[skill choice]` (house: at least 1.1, up to 1.8) | `plain line` |

Hierarchy: frame 1.45 > object = arrow = dashed group 1.1 (the white sticker 1.2 shows only on fills) > badge 0.5.

#### 5.2.2 Corner radii

| Element | Spec | `housediagram.sty` |
|---|---|---|
| Input square | **0 (sharp)**, outlined in black 1.1 `[current]` | `input square` |
| Square data tokens and chips | **2.0** (0.13 × height) or sharp; one choice per figure `[current]` | `token`, `chip` |
| Module boxes, op boxes, the model block | **2.6** (0.13 × height, within 2.0-3.2) `[current]` | `hd base` and every module style |
| Lanes | 3.4 [2.6-5.8] `[current]` | `lane` (`\hdlane`) |
| Frames, cards, big panels | **5.8** [4.2-10]; legend card 6.6 `[current]` | `heavy frame`, `perforated card`; `card` 6.6 |
| Dashed container | 4.6 `[current]` | `dashed group` |
| Elbow corner of a connector | **4.2** [3.0-5.1] at the centre line, about 3.7 × line width `[current]` | every connector style (`rounded corners=4.2pt`) |

#### 5.2.3 Shadow

| Quantity | Spec |
|---|---|
| Model | a Gaussian-blurred copy of the shape, offset down-right, neutral black, multiplied over tinted fills `[current]` |
| Offset | right **1.1** [0.83-1.32], down **1.5** [1.3-1.9] (a little steeper than 45°) |
| Blur σ | **1.0** [0.85-1.2], about the object stroke |
| Opacity | **0.25** [0.23-0.26] |
| Result | about 0.23 dark at the bottom edge, visible 3.0-3.8 below and 2.6-3.2 to the right, faint (0.02-0.04) at top and left, not zero |
| Profile below the bottom edge | d = 0 / 0.5 / 1 / 1.5 / 2 / 2.5 / 3 → 0.23 / 0.21 / 0.17 / 0.125 / 0.08 / 0.04 / 0.017 |
| Gets a shadow | every **free-standing** object: small tokens and chips, module boxes, the input square, model blocks, callouts, white cards, panels and frames `[current]` |
| Gets none | objects inside a card, a frame, a dashed container or a legend (`no shadow` on the scope); lanes; circles; arrows; text `[current]`; legend icons `[skill choice]` |
| Optional | a lighter line shadow (peak 0.14-0.26, about 1 below) on the one solid signature loop arrow `[some]` (not in the package) |
| Stacks | each card has its own shadow, so darkness builds to about 0.4 under 4 layers `[current]` (`\hdstack`) |

`hd shadow` (objects) and `hd frame shadow` (cards and frames) draw eight concentric translucent rounded rectangles under the shape, so the PDF stays pure vector. Measured on the render: 0.23 / 0.20 / 0.17 / 0.125 / 0.05 / 0.03 / 0.01 at d = 0 … 3 pt below a free-standing box, and still visible over a lane. Every box style carries its own shadow: give each node one box style, or the shadows stack.

#### 5.2.4 Arrowheads and connectors

| Quantity | Spec | `housediagram.sty` |
|---|---|---|
| Head | filled triangle with a notched back: length **5.2** [4.5-5.8], width **5.3** [4.1-5.7] (width ≈ length), 27° half-angle at the tip, notch 0.2 × length `[current]` | `hdhead`: `Stealth[length=5.2pt, width=5.2pt, inset=1.25pt]` |
| Shaft | butt end hidden inside the head; the barbs overhang the shaft | `line cap=butt` in every connector style |
| Tip | lands on the target's outer stroke edge (0-0.2 gap) | TikZ ends the path on the node border, the centre of the outline |
| Routing | orthogonal with rounded elbows (r 4.2); lines cross each other and frames with no jump-overs; long returns may be smooth arcs | `-|` and `|-` paths |
| Fan-out / merge | one trunk splits through rounded elbows like a curly brace; several connectors merge into one head the same way `[current]` | two paths from one stub (`-- ++(8pt,0) |- (target)`), or `brace` |
| Colour | grey `#595959` for data / control flow, or the role colour; black only for one signature loop `[current]` | `data flow`, `role flow=<colour>` |

#### 5.2.5 Dashes and dots

| Style | Spec | `housediagram.sty` |
|---|---|---|
| Dashed arrow / group | dash = gap = **3 × line width** (3.3 / 3.3 at 1.1), flat ends; dashed arrows keep a solid head; dashes follow rounded corners `[current]` (`[older]`: 4:3) | `score flow`, `dashed group` |
| Dotted "invalid" outline | **square** dots: dot = line width (1.1), gap = 2 × line width (2.2), flat ends; corners become small wedges `[current]`. Round dots read as a string of beads | `chip dropped`, `token dropped` (16 periods around a token, so a dot sits on every corner) |
| Guide line | square dots, never the dash pattern of a failure mark | `guide line` |
| White perforation | 1.8 white, 5 / 5, notching the shadow | `perforated card` |
| Lane state edge | 0.5, 4.1 / 4.1, light accent colour `[some]` | a `lane` with `draw=<colour>, line width=0.5pt, dash pattern=on 4.1pt off 4.1pt` |

TikZ starts a dash pattern at the start of its path: check in the PNG that a dashed arrow leaves its source with a dash and ends on a full gap before the head, and that a dotted outline has no merged dots.

#### 5.2.6 Panels and padding

| Surface | Fill | Edge | Shadow | Padding (`housediagram.sty`) |
|---|---|---|---|---|
| Frame panel | white | solid `#595959` 1.45 | yes | object to frame **9.6** [8.7-11] (`heavy frame`, 9.5) |
| Perforated panel | white | white dashed 1.8 (5 / 5) | yes, notched | panel to lane about 3-9 (`perforated card`, 7) |
| Lane (row strip) | `#F6F6F6` | **none** | **none** | 6.2-6.6 horizontal, 1.9-5.7 vertical; 1.9-3.1 between lanes (`lane`, 6 / 4; the reference figures leave 2.5 between lanes) |
| Borderless card | white | none | yes | (`card`, 7) |
| Dashed group container | none | 1.1 dashed 3.3 / 3.3 | none | **4.6** (`dashed group` uses 3, so a group fits inside a lane's padding `[skill choice]`) |
| Magnifier / zoom surface | pale `#F0F6F8` | none | none | continues as a trapezoid wedge to the part it enlarges `[some]` (drawn by hand) |

A heavy 1.45 frame takes frame padding; a lane takes lane padding. A heavy frame with lane padding looks cramped.

#### 5.2.7 Sizes and spacing

| Quantity | Spec |
|---|---|
| Square data token | **15 × 15** [14.5-18.5]; node circles the same size (`token`, `operator`) |
| Wide one-line item | about 57 × 15 (aspect 3.8) (`chip` with a `text width`) |
| Op box | about 28 × 18 (aspect 1.5, token height), or a 10 × 10 badge (`badge`) |
| Model block aspect | **1.6** [1.55-1.63] |
| Gap between sibling tokens in a row | **3.3** [1.7-6.5]; below about 3 the 1.1 right shadow fills the gap and the row looks crowded; the reference figures leave 4.5 between shadowed chips and 3.5 between flat ones `[skill choice]` |
| Gap between rows | **7.8** [6.8-9.6] |
| Stack offset per card | 0.85 (dense stack) to about 4.5 (legend stack), up and to the right (`\hdstack`: 1.8) |
| Ellipsis | bold 2-pt square dots at 4.5 pitch |

#### 5.2.8 Text

| Quantity | Spec |
|---|---|
| Label size | **one** size per figure, **6.9** [5.4-7.2] in the `[current]` figures (0.68-0.71 × the 10-pt caption); the package uses three tiers, 9 / 7.5 / 7 pt (`headline`, `header`, `key label` / `note`), so a headline reads at arm's length `[skill choice]` |
| Face | bold sans (Helvetica) `[current]`: TeX Gyre Heros; `[serif]` option: Times text and math |
| Weights | entity and legend labels **bold**; process / action labels *italic regular* (`note`); op labels regular |
| Math | LaTeX math at **1.1** [1.08-1.13] × the label glyph (`\DeclareMathSizes` in the package: 1.08 ×); Computer Modern `[current]` (Latin Modern) |
| Role tags | superscript role tags in the role colour, one step brighter than the stroke `[some]` (`\textcolor{hdrose}{...}`) |
| Text-to-box | item box height 3.1-3.6 × the cap height; module 4.9-6.8 ×; about 2 pt side padding |
| Colour | black; white on dark, filled or gradient modules; state names in a legend coloured like their stroke |

#### 5.2.9 Legend

| Quantity | Spec |
|---|---|
| Frame | **none**, or a white card with **no edge** and the full shadow, r 6.6 `[current]`; the package: none (`legend row`) `[skill choice]` |
| Icons | **1:1** with the real items (tokens 15, arrows about 22 long) `[current]`; the package: flat icons 11 × 9 (`key box`), a 9.5-pt two-state stack (`key pair`), 17-pt arrows (`key arrow`), marks (`key mark`) `[skill choice]` |
| Spacing | icon-to-label **6-8** (up to 12); entry-to-entry at least **2 ×** the icon gap (`legend row`: 14) so each label clearly belongs to its own icon; row pitch **18-20** |
| Labels | bold, one line, to the right of the icon; `key label` fixes height and depth, so labels with and without descenders or math share one baseline |
| Two-state entries | a stacked-card icon: front card = active state, back card = inactive `[current]` (`key pair`) |
| Placement | a strip under the diagram, a right column, or a top corner `[current]`; the package: one row centred under the diagram or a top corner, never above the content `[skill choice]` |

#### 5.2.10 Finishing touches

These make the figures read as cards lifted off the page `[current]` unless marked:
1. **The shadow itself** (§5.2.3), on every free-standing object including small tokens (`hd shadow`). It is the largest single part of the look.
2. **Stacked cards** for sets, multiplicity or two states, each layer with its own shadow (`\hdstack`).
3. **Ghosting of inactive steps**: the frame and its shadow stay at full strength; everything inside is drawn in lighter **opaque** colours, not transparency, so overlaps do not darken (`ghost`, `ghost flow`).
4. **Invalid or skipped items**: square-dot outline (§5.2.5), not transparency (`chip dropped`, `token dropped`).
5. **Perforated white panel**: white dashes cut notches into the shadow (`\hdperforated`).
6. **White "sticker" outline** on filled shapes; stroked shapes stay white with a coloured 1.1 stroke (`hero`).
7. **Colour gradient** on the one model box shared by two branches, from one branch colour to the other, left to right, with a white bold label; one per figure (`hero gradient`).
8. **Tiny operator badges** (10 × 10, 0.5 stroke, r 2.0) sitting on dashed score lines, one math symbol inside (`badge`).
9. **Maths tags in the role colour**, one step brighter than the stroke `[some]`.
10. **Optional light shadow on the main solid loop arrow**; never on dashed or grey arrows `[some]`.
11. **Curly-brace fan-out and merge** of connectors from one trunk (forked paths, `brace`).
12. **Magnifier wedge** from a highlighted part to its enlarged view `[some]`.
13. **Highlight pills** (pale tint, 0.76 edge plus a paler 0.46 halo) around a group of tokens `[some]`.
14. **Annotation text** in a distinct voice (coral / teal italic, `note, text=hdcoral`), only for "what goes wrong" callouts, never for labels `[some]`.
15. **Lane with a 0.5 dashed state edge** when its content meets a condition `[some]`.
16. **Bold dot ellipsis** for skipped rows.

**Check on every new diagram** (the issues that came up while the reference set was redrawn):
- Width within 3 % of the target (the build fails otherwise): change a gap or a width, never scale the figure.
- A chip whose text is wider than its `text width` minus the tag wraps at a space: widen the chip, shorten the example, or tie the words (`Prove~${\sqrt{2}\notin\mathbb{Q}}$`).
- A label never sits on an elbow or on a line: on a line use `pill`, beside it `note` with `above` / `below`; leave a horizontal run long enough for its label.
- `(a |- b)` takes named nodes or coordinates only: store a `calc` result with `\coordinate` first.
- Equal halves in a contrast figure: the same module widths on both sides, and the divider centred between the two callouts.
- Box proportions: do not stretch a module box to a lane's height unless the lane holds only modules; a single item is a square token.
- Legend centred under the figure (`\matrix[legend row, anchor=north] at (centre)`), all labels on one baseline.

## 6. Placement and captions

Placement:
- `[t]` in the main text; `[ht]`/`[!ht]` in the appendix `[all]`.
- NeurIPS/ICLR three panels: `figure*[t]` with three `subfigure[b]{0.32\textwidth}` + `\hfill` `[most]`; two unequal panels tuned to equal heights (e.g. about 0.4 + 0.5) `[some]`.
- NeurIPS space saver: `wrapfigure{r}{0.52\textwidth}` holding two `subfigure`s at `0.49\linewidth` `[venue: NeurIPS]`. Never wrapfigure in two-column ICML; there use `figure[t]` at `\columnwidth`, pairs as `0.49\columnwidth`, method figures `figure*[t]`.
- Negative `\vspace` inside every float `[all]`: `-6` to `-20pt` between image and subcaption; `-5` to `-8pt` before `\caption` (**never at ICML**: its kit and sty require ≥0.1 in before and after captions, `venues.md` §1.2); `-10` to `-20pt` after `\label`. This assumes text follows the float; when two floats stack in one column, the end `\vspace` of the upper one eats `\floatsep` (12 pt) and the floats touch (skill's ICML test build: -12 pt left 0.01-0.03 in under the caption) — drop the upper float's trim.
- Page-1 figure: a two-panel results figure right after the abstract (`[!h]` before `\section{Introduction}`), then the concept teaser inside the intro `[some]`; or the teaser lands on top of p2 by ordinary float placement `[some]`. `templates/main_skeleton.tex` forces the p1 position with `\suppressfloats[t]` after `\maketitle` `[skill choice]`.
- Labels `fig:<name>`, panels `fig:<name>-a/-b`, appendix floats `fig:apdx-<name>`; stock names `fig:teaser-figure`, `fig:main-figure`, `tab:main-table` (`latex_source.md` §2); "Figure~\ref{}" by hand.
- Float position in the source: inline, in the subsection of its first `\ref` `[most]`, right before the paragraph that first cites it, or right after it when that paragraph ends the subsection (`latex_source.md` §5).

Captions (below figures, `[all]`):
1. Open with a title-like phrase ending in a period: "Coverage over training.", "The DIVA loop.", "Norms of the learned filter weights."
2. Then a takeaway sentence `[most]`: "Filtering with DIVA matches the unfiltered model's final accuracy in 40\% of the steps, and the diversity scorer adds a further gain late in training."; experiment captions may stop after the title phrase `[some]`.
3. Framework captions walk through the procedure in 3-5 sentences with math, in arrow order: "Given a batch $\mathcal{B}_k$, the difficulty scorer rates every example ... The diversity scorer then ... The two scores are multiplied into sampling probabilities (\ie $p_t(x_i) \propto w_i\,q_{c(i)}$), and the sampled batch updates the model." `[some]`; or one sentence per input type in the order of the twin concepts: "For in-distribution inputs, ... For shifted inputs, ..." `[some]`.
4. Multi-panel captions refer to panels as `(\ref{fig:a}) ... (\ref{fig:b}) ...` `[most]`.
5. Subcaptions: short noun phrases ending with a period ("Accuracy on Set-B.") `[current]`, or a model name.
6. State non-obvious encodings ("The dotted line marks random selection."); cite datasets inline.

Text boxes as figures `[most]`: prompts and case studies are tcolorboxes captioned as Figures, prebuilt in a savebox (`\begin{lrbox}{\PromptBoxA}` + `gptpromptbox` + `\usebox`); case studies use `promptbox` with `badcasebox`/`goodcasebox` and response rows `\textbf{$w/o$ Method:} \quad \textcolor{bad}{...}` / `\textbf{$\phantom{/o}w$ Method:} \quad \textcolor{good}{...}` `[some]`. Compiling examples in `templates/chapters/7_appendix.tex`.

### 6.1 Two columns `[venue: ICML]`

**Two-column float budget.** Wide floats (`figure*`/`table*`) are reserved for the framework figure, a large method illustration and the main table; everything else is single-column.

| Float kind | Environment and width | Printed width | Placement |
|---|---|---|---|
| Teaser (problem or concept) | `figure[t]`, `\columnwidth` | 3.25 in | p1, top of the right column; first thing in the introduction file |
| Prompt / text box | `figure[t]`, box at `\linewidth`, `\small\ttfamily` | 3.25 in | method section, column top; no end trim when an algorithm stacks below |
| Framework diagram | **`figure*[t]`, 0.9\textwidth** | 6.1 in | source about one column-page before the text that discusses it |
| Algorithm | `algorithm[t]`, one column, novel lines shaded | 3.25 in | column top (~0.55 column) |
| Large method illustration | **`figure*[t]`, `\textwidth`** | 6.75 in | next page top after its definition |
| Main results table | **`table*[t]`**, `\shrinktofit{0.93\linewidth}` | ≤6.3 in | next page top after its definition |
| Single result / analysis / ablation plot | `figure[t]`, `\columnwidth` | 3.25 in | in the subsection that cites it |
| Secondary table (stress setting, variant) | `table[t]`, 0.93\columnwidth | ≤3.0 in | in the subsection that cites it |
| Plot pair | `figure[t]`, 2 × `subfigure[t]{0.49\columnwidth}` + `\hfill` | 1.59 in each | in the subsection that cites it |

Float load: about a dozen floats, at most three of them wide, take about 2.6 of the 8 pages (about 3.5 columns of column floats plus 0.85 pp of wide floats). Appendix (after `\onecolumn`): `[ht]`/`[!ht]`, pairs at `0.49\columnwidth` (= 3.3 in), triplets at 0.25.

Rules:
- **Wide only for method diagrams and the main table**: framework `figure*` at 0.9\textwidth (0.88-1.0 in one-column venues), a large method illustration at `\textwidth`, the main results `table*` `[all]`. Result, analysis and ablation plots are single-column; pairs are two `subfigure`s at 0.49\columnwidth with 2-4-word subcaptions.
- **Never `wrapfigure`** in two columns (the official template has none; the NeurIPS space saver does not port). No `\textwidth` inside a one-column float or a main-text `tcolorbox` (it overflows into the other column).
- **A `figure*`/`table*` never prints on the page where it is defined**: at the earliest, it goes to the top of the next page. Put its source about one column-page before the text that discusses it, so each wide float prints one page after its definition. Two wide floats can share a page top (skeleton p4); space them to avoid a page with little text.
- **The teaser can float above the abstract** when the abstract is short: a `[t]` teaser goes to the top of the left column. `main_skeleton_icml.tex` adds `\suppressfloats[t]` after `\printAffiliationsAndNotice{}` `[skill choice]`; delete it once the text fills column 1.
- **Negative `\vspace` inside floats only, and never before `\caption`**: the kit asks for ≥0.1 in before and after every caption and the sty sets `skip=0.1in` (K-ICML26 p3-4; `icml2026.sty` l.681). Pre-caption trims (-8 pt on a teaser, -3 to -6 pt in a `figure*`, -6 pt after subfigure groups) squeeze that gap to about 0 `[fix]`; the templates drop them. Keep only the trims after the label: -12 pt, -17 pt after `table*`, -10 pt after `table`, as long as ≥0.1 in remains before the body text. When a float sits directly above another float in the same column, drop its trim: in the skeleton render a -12 pt trim left 0.013 in between a text box's caption and the algorithm below it, and 0.033 in between a plot's caption and the table below it; without the trim both gaps exceed 0.1 in. Measure on the rendered page, since stacking depends on the final layout. The template forbids "reducing the vertical spaces"; the layout guard does not check float `\vspace`, but never touch `\textheight`, `\baselinestretch`, section skips or display skips.
- **Sizes**: draw single plots at 7.5-8 in (square panels 3.75 in) with 16 pt ticks, so they print at about 7 pt. Export diagrams at ≥300 px per printed inch; labels inside a diagram should print at ≥6 pt (about 13 pt on a 13.3-in slide for a 6.75-in figure).
- **Tables**: `\shrinktofit{w}{}` instead of `\resizebox`; no gray shading behind text (ICML kit: "text should not appear on a gray background"; use `oursrowblue` or the metric tints `qualityrowcolor`/`costrowcolor`); `\arraystretch` inside the float; `$\uparrow$`/`$\downarrow$`, not Unicode.
- Captions 9 pt with an italic label ("*Figure 3.*", set by the template). Experiment captions are short title phrases; framework captions walk the procedure in 1-3 sentences with "(1) … (2) …".
- Narrow-column displays and algorithms: `math.md` §3 rule 13 and §7.

## 7. Tables

| # | Rule | Evidence |
|---|---|---|
| 1 | booktabs `\toprule/\midrule/\bottomrule` + `\cmidrule(lr)` | `[all]` |
| 2 | Caption ABOVE, `\label` after the tabular, negative `\vspace` at the end (`-16`/`-17pt`) | `[all]` main text; required by all three venues |
| 3 | Width only by scaling, never `\small`: the house writes `\resizebox{w\linewidth}{!}{...}`, inside `\makebox[\linewidth][c]{...}` at 0.92-0.93 `[current]`. The templates use `\shrinktofit{w\linewidth}{...}`, which scales only when the tabular is wider than w (pitfall 4) | `[all]` |
| 4 | Main results in `table*` even in one-column venues | `[all]` |
| 5 | A vertical rule after the row-label column(s); also before an Avg. column when there is one `[some]`, and between dataset groups `[older]` | `[most]` |
| 6 | Bold = best (ties both bold); in before/after tables, bold = improved over base `[some]` | `[all]` |
| 7 | Two-level header: spanning group label over column names | `[all]` |
| 8 | `\renewcommand{\arraystretch}{0.9}` — put it INSIDE the float (setting it before the float leaks into later tables `[fix]`) | `[most]` |
| 9 | Bold header row | `[current]` |
| 10 | Rows "+ Method" relative to a base-model row | `[some]` |
| 11 | Our row shaded: `\cellcolor{oursrow}` (gray 0.95) per cell when a `\multirow` column exists; `\rowcolor{oursrowblue}` (#EFF6FB) otherwise. Quality + cost tables shade metric rows instead (rule 22). ICML: blue only (the kit forbids text on gray) | `[some]` each |
| 12 | `\midrule\midrule` between backbone/scale blocks | `[current]` |
| 13 | Scale in a `\multirow` first column, `cl|...` | `[some]` |
| 14 | `\cmidrule(lr){2-N}` separates Base/SFT from trained rows | `[some]` |
| 15 | Unit + direction in metric headers when a table mixes metrics or directions: "Acc. \% ↑" / "Time min ↓", "ECE \% ↓"; a one-metric main table has no arrows `[some]`. Write `$\uparrow$`/`$\downarrow$`: this corrects the common practice of typing the Unicode arrow, which drops out under XeLaTeX with Times (pitfall 2) `[fix]` | `[some]` |
| 16 | Precision: 2 dp for %-metrics `[most]`; 3 dp for scores in [0,1] `[skill choice]`; integers for count metrics `[some]` | see cell |
| 17 | Avoid: underline for 2nd best, `*` significance marks, a percentage-improvement row | `[current]` |
| 18 | `Name (\citeauthor{}, \citeyear{})` row labels: ICLR `[venue: ICLR]`, also seen in NeurIPS numeric mode `[some]`. ICML is also author-year, but the pattern is untested there | `[venue: ICLR]` |
| 19 | Coloured change superscripts `\textbf{71.40}$^{\color{+}+1.15}$` (red = up, blue = down) for before/after tables | `[some]` |
| 20 | Grayed ablation row `\ablation{}` | `[some]` |
| 21 | Appendix hyperparameter table: `tabularx` `l|X`, header `\multicolumn{1}{c}{\textbf{Hyperparameter}} \vline & ...`; values also in prose (caption below in the sources; move it above `[fix]`) | `[current]` |
| 22 | Quality + cost: two metric rows per method tinted by metric (`qualityrowcolor` pink, `costrowcolor` blue), no ours-row shading, no Avg column | `[some]` |

Captions: one noun-phrase sentence ("Accuracy comparison across the evaluation suites.") plus the bolding rule when anything is excluded ("The best result in each column is \textbf{bold}; ablation rows are not ranked."). Appendix tables: plain headers, generic captions.

**Main-table variants.** The house does not use one main-table form; choose by the claim. Each variant is `[some]`: they are house choices for a given claim, not venue rules, and T2's "(Ours)" appears only in a baseline-heavy table.

| Variant | Layout | Avg. column | Our row | Arrows | Choose when |
|---|---|---|---|---|---|
| T1 scale-grouped | `cl|...`, `\multirow` scale, Base/SFT above `\cmidrule`, header "Datasets" | none | `+ X`, gray `\cellcolor{oursrow}` per cell, no "(Ours)" | none (one metric) | one model family at 2+ scales, 1-2 baselines, one metric |
| T2 per backbone | `l|...|c`, one block per backbone, `\midrule\midrule` between | yes, behind `|`, header "Avg" + metric + ↑ | bold "+ X (Ours)", `\rowcolor` blue; grey "+ X (w/o C1) (Ablation)" row above it | in the spanner and the Avg header | ≥3 baselines from ≥2 families, several backbone families, one commensurable metric |
| T4 quality + cost | `cl|c|...`, two metric rows per method | none | none; metric rows tinted pink/blue | per metric row ("Acc. \% ↑", "Time min ↓") | efficiency is a co-claim |

- Avg. column: include it only when all columns share one metric and the claim is about overall performance `[some]`; omit it in a scale-grouped table with one baseline or when rows carry two metrics.
- "(Ours)": only in baseline-heavy tables, where the row must stand out among ≥3 baselines `[some]`. With one or two baselines, the method name plus shading is enough.

Template choice (`templates/tables.tex`): T1 scale-grouped main results with `\pend` cells · T2 per-backbone "+ X (Ours)" with Avg column · T3 before/after with coloured superscripts · T4 quality + cost paired tinted rows · T5 ablation · T6 hyperparameter sweep · T7 appendix hyperparameters · T8 dataset statistics. `templates/tables_preview.tex` renders them one per page.

Pitfalls (verified by compiling):
1. `\rowcolor` paints over a `\multirow` label typeset in an earlier row → use per-cell `\cellcolor` from column 2.
2. Unicode ↑/↓ vanish under XeLaTeX with the venues' Times ("Missing character ... ptmr8t") → write `$\uparrow$`/`$\downarrow$` `[fix]`.
3. `\arraystretch` set outside the float leaks everywhere.
4. `\resizebox` also ENLARGES narrow tables: a 6-column table at 0.93\linewidth in a one-column venue prints larger than body text. Use `\shrinktofit{w}{...}` (preamble B8), which scales only when the tabular is wider than w, or give `\resizebox` about 0.75\linewidth for a narrow table (0.93 is meant for `\columnwidth` in two columns).
5. booktabs rules interrupt vertical bars (visible gaps); the house accepts this.
6. `tabularx` without an `X` column gives underfull boxes; use `l|>{\centering\arraybackslash}X`.

## 8. Preamble notes (full file: `templates/preamble.tex`)

- `\PassOptionsToPackage{table}{xcolor}` before the venue style (ICLR's eso-pic and ICML's style load xcolor; otherwise "Option clash for package xcolor").
- `\usepackage[T1]{fontenc}` under both engines; otherwise XeLaTeX (tectonic) falls back to Latin Modern.
- NeurIPS 2026 camera-ready needs `[main,final]`; `[final]` alone stops at `\@trackname`.
- `\definecolor{lightgray}{gray}{0.95}` silently redefines xcolor's `lightgray` `[fix]`; the preamble uses `oursrow` instead.
- `upgreek` broke tectonic (missing `eurm`); omitted.
- No cleveref, no method-name macro.

## 9. Checklist

- [ ] One PNG per panel via `plot_style.apply()` + `save()`; bold Times; four-sided box; no plot titles; unit in parentheses `[all]`. Printed ticks ~7 pt; 600 dpi and a dashed y-only grid `[current]`; ICML: include the PDF. Compare with `assets/plots/`.
- [ ] Blue `#649CD8`/`#BFD8EF`, pink `#FF85A1`/`#FFDAE2` `[current]`; saturated = ours `[all]`; one colour = one meaning across diagram, plot and text `[most]`. Metric-picks-the-hue is an option `[some]`.
- [ ] Framework: a TikZ source on `housediagram.sty` (or a slide-tool figure that matches it), built by `render_diagram.py --check` at the printed width and included unscaled; white rounded modules with 1.1-pt role outlines (rose = trainable, navy = fixed; amber only for score / feedback arrows; teal / coral only for good / bad marks), exactly one filled focal module, no earthy fills, few light lanes, at most one heavy frame, black text, bold sans labels with LaTeX math, no free-floating formula, a compact legend row (or none), the running example of the teaser; metrics white ≥ 0.72, earthy ≤ 0.05, ≤ 3 hue families, mean-sat 0.25-0.75; `figure*[t]` at 0.88-1.0\textwidth; caption walks the arrows. Compare with `assets/diagrams/`.
- [ ] Diagram details at print size (§5.2), all built into the package styles `[current]`: one 1.1-pt stroke on every object and arrow, 1.45-pt `#595959` frames; the Gaussian shadow (1.1 right, 1.5 down, σ 1.0, 25 % black) on every free-standing object, none inside cards, frames or legends; notched heads 5.2 × 5.2; elbows r 4.2; dashes 3.3 / 3.3, square dots 1.1 / 2.2; radii input 0, token 2.0, box 2.6, lane 3.4, frame 5.8; lanes `#F6F6F6` with no edge; tokens 15 × 15, ≥ 4 pt between shadowed siblings; type 9 / 7.5 / 7 pt, math 1.08 ×; legend labels on one baseline; the PNG checked by eye for wrapped chips, labels on lines and arrowheads off their targets (§5.2 check list).
- [ ] Captions: title phrase + takeaway; subcaptions end with a period; `(\ref{fig:a})` for panels.
- [ ] Tables: `table*`, caption above, booktabs + `|` after the labels, `\shrinktofit` (or `\resizebox` only for a table wider than the line) in `\makebox`, bold headers, bold best, variant chosen by the main-table rule (T1 / T2 / T4), `\midrule\midrule` between scales, `$\uparrow$` (not Unicode), `\arraystretch` inside the float; table text never larger than body text.
- [ ] Axis-label case consistent within the paper (Title Case or sentence case).
- [ ] ICML: wide floats only for method diagrams and the main table; `figure*` source one page early; no wrapfigure; no `\textwidth` in column floats (§6.1).
