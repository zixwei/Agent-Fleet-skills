# LaTeX source habits and naming

Read at Step 0 and whenever you create a file, label, figure file, macro or bib entry, and before the camera-ready switch (revision macros). This file covers the source conventions of the house style; its rules are reflected in SKILL.md "Source (LaTeX)", `structure.md` §3, `prose.md` §9 r21-22, `figures_tables.md` §2, the template labels `fig:teaser-figure` / `fig:main-figure` / `tab:main-table`, and the duplicate-label lint in `check_paper.py`.

Evidence labels as in SKILL.md: **[all]**, **[most]** (the style), **[some]** (an option, not a checklist item), **[current]** / **[older]** (practice that changed over time: follow `[current]`), **[venue: X]** (depends on the venue), **[fix]** (a common slip that the skill corrects, not copies), **[skill choice]** (a template helper that is not house practice).

## 0. Method

The conventions were measured on the compiled chapters only, i.e. the files the main `.tex` really `\input`s; backups and `*_old` files count only where a rule is about keeping them. Label/ref extraction, float-to-first-`\ref` distance, comment classification, sentence-per-line and bib-key classes were counted with small scripts, and every `\ref` resolved to a label.

## 1. Scope: what this file adds to the rest of the skill

| Topic | Where the skill also has it | What this file adds |
|---|---|---|
| One sentence per line | SKILL Step 0.4; `structure.md` §3 | also: no hard wrap at all (§9) |
| Old wording kept as `%` | SKILL Step 0.4; `structure.md` §3; `prose.md` §9 r22 | old sentences stay *next to* their replacement (above, below or between two live sentences), not always directly above it. Only colour values (§7.4) and the anonymous URL (§7.5) are consistently "old above new" |
| Status/co-author macros | `structure.md` §3; `preamble.tex` B4 | naming scheme, drafting colours, how the camera-ready neutralises them, `\revise{}` / `\rebuttal{}` / `\rebuttalcaption{}`, `\iclr{}`, which ones stay coloured on purpose (§7) |
| Rebuttal additions | `prose.md` §9 r21 | tied to ICLR's revised-PDF discussion phase (§7.3) |
| Title candidates kept | `structure.md` §2; `main_skeleton.tex` | keep only this paper's candidates (§7.5) |
| Figure folders | `figures_tables.md` §2 | layout, stems and marking styles for superseded files (§4) |
| Labels | `figures_tables.md` §6; `structure.md` §7 (`apdx:`) | the full scheme (§2) |
| Cross-reference typing | SKILL "Figure~\ref{}", "Table~\ref{}" | house practice types a plain space; the skill writes `~` (§3) |
| `.bib` | SKILL "BibTeX keys are the short names"; `templates/references.bib` | file name, entry source and fields, topic headers, the conversion script (§8) |
| Floats in chapter files | `figures_tables.md` §6 (options, `\vspace`) | *where* in the file a float goes (§5) |
| Macro placement | `figures_tables.md` §8 | the venue rule (§6) |
| `\input` vs `\include` | `structure.md` §3 layout | stated explicitly (§11) |

## 2. Label naming

### 2.1 Dominant pattern **[most]**

`<type>:<key-words>`, key words in lowercase joined by hyphens, taken from the heading or caption:
`sec:scorer-design`, `sec:key-idea`, `fig:coverage-dynamics`, `tab:transfer-perf`, `eq:budget-constraint`, `apdx:impl-details`.

Hyphens replaced underscores **[current]** (all-underscore labels are **[older]**). Underscores survive where the label copies a file stem or a model/dataset id: `fig:acc_set_a_s` = `acc_set_a_s.png`, `tab:transfer_model_s_sizes`, plus a few fixed names carried over from earlier projects. Upper case appears only in proper names (`eq:CaliLoss`, `fig:acc_Set_A`). Rule: lowercase + hyphens; keep a model/dataset token as spelled when the label mirrors a figure file.

### 2.2 Prefixes

| Prefix | Used for | Evidence |
|---|---|---|
| `sec:` | main-text sections **and** subsections (no `subsec:`) | [all]; a stray `subsec:` is rare [some] |
| `apdx:` | appendix sections **and** subsections | **[most]**; `sec:app_...` in the appendix is **[older]** |
| `fig:`, `tab:`, `eq:` | floats and displays | [all] |
| `alg:` | algorithm blocks | [most]; `algo:` or a `def:` label on an algorithm are slips [fix] |
| env name | formal results: `lemma:<name>` | theory papers [some]; a bare `\label{theorem}` is a slip [fix]. `thm:` is not house practice; the template's `thm:`/`def:`/`lemma:` follows the env-name rule [skill choice], keep it |

Unprefixed labels are slips: at most one per paper in practice [fix].

### 2.3 Panels, appendix copies, stock names

- Panels of a multi-panel float: parent `fig:<name>` + `fig:<name>-a/-b/-c` **[some]**; otherwise a model/dataset suffix (`fig:coverage-layer-s`, `fig:acc_set_b_l`) or a descriptive name (`fig:easy`/`fig:hard`). Use `-a/-b` for panels (matches the `(\ref{fig:x-a})` caption convention), model suffixes for per-model grids.
- Appendix floats are marked: prefix `fig:apdx-<name>` (`fig:apdx-coverage-by-layer`, `fig:apdx-sweep`) or suffix `-apdx` (`fig:ablation-apdx`) **[most]**. A main-text version of an appendix figure gets `main-`/`-main` (`fig:main-coverage-by-layer`, `fig:alpha-effect-main`) [some].
- Stock labels that recur across papers (reuse them; they make the checklist and cross-paper copying easier) [some each]:

| Label | Use |
|---|---|
| `fig:teaser-figure` | teaser (a longer `fig:teaser-<name>` also occurs) |
| `fig:main-figure` | framework figure |
| `tab:main-table` | main results table (the underscore form is **[older]**) |
| `sec:main-performance` | RQ1 subsection |
| `sec:limitations` / `sec:conclusion` | back sections |
| `apdx:impl-details` / `apdx:implementation-details` | implementation details |
| `apdx:prompts` | prompts |
| `apdx:experiments` | additional experiments |
| `apdx:case-study` | case study |
| `apdx:broader-impacts` / `apdx:broader` | NeurIPS Broader Impacts |
| `tab:hyper-train` | appendix hyperparameter table |

### 2.4 Where `\label` goes **[all]**

- Same line as the heading, after a space: `\subsection{Key Idea} \label{sec:key-idea}` (nearly every labelled heading).
- Immediately after `\caption{...}` (no exception among labelled captions).
- On the `\begin{equation}` line: `\begin{equation}\label{eq:coverage-term}` **[most]**. Most displays carry no label; label the ones you cite.

### 2.5 Proofreading slips to catch **[fix]**

- Duplicate labels: the same `eq:` label defined in both the Preliminary and the Methodology, or one `fig:` label reused for several prompt figures in the appendix. `check_paper.py` flags duplicates.
- A framework figure that is never `\ref`'d. Every main-text float should be cited in the text.

## 3. Cross-reference typing

- House practice types the reference word by hand and puts a **plain space** before `\ref` **[all]**; ties appear mainly in newer appendix text **[current]**.
- Skill rule: write `Figure~\ref{}`, `Table~\ref{}`, `Section~\ref{}`, `Appendix~\ref{}`, `Equation~\ref{}` everywhere [fix: a tie only stops a line break before the number and does not change the look]. SKILL.md, the `preamble.tex` comments and every template chapter (`templates/chapters/`, `templates/chapters_icml/`) use the tie.

## 4. Figure files and folders

**[all]** unless marked.
- Paths are written from the project root with the extension and no `\graphicspath`: `\includegraphics[width=\columnwidth]{figures/exp/cost_quality/set_a.png}`. PNG dominates; JPG and PDF panels are rare. This skill's diagrams are vector PDFs built from TikZ sources kept in `figures/src/` (`figures_tables.md` §5): include `figures/framework/<name>.pdf` at the width it was drawn at `[skill choice]`.
- One topic folder level under `figures/`: every data plot sits in a subfolder; at most a diagram or two sit at the root. Folders are named by **what is shown**, never by figure number (no `fig1.png`):
  - method paper: `figures/{coverage,difficulty,ablation,sweep}/`
  - findings paper: finding-named folders, e.g. `figures/{Encoding,Editing,Transfer}/<sub>/`
- Current layout **[current]** (use it): diagrams in `figures/framework/` (or `method/`), plots in `figures/exp/<analysis>/`.
- File stems: content first, then model/dataset/setting tokens, mostly snake_case: `coverage_vs_budget_s.png`, `set_b_tau=0.5.png`, `acc_set_a_s.png`. Diagrams are named after the method or their role: `teaser.png`, `key_idea.png`, `main_figure.png`, `<Method>_teaser.png`. `=` and extra dots inside stems (`..._model_v1.5_tau=0.5.png`) are safe only because the extension is always written; keep writing it.
- Stale files are kept, never deleted **[most]**; many projects end with a third of `figures/` unused. Marking styles: `(drop)` suffix (`sweep_detail(drop).png`, folder `sweep(drop)/`), `_old` (`framework_old.png`, `coverage_old/`), version suffix `_v2`/`_v3` (`cost_quality_v3.png`). Pick one: `_old` for a superseded version; `(drop)`-style removal is equivalent. **[most keep; marking varies]**
- A placeholder image at the root of `figures/`: `figures/Placeholder.png` **[most]**, referenced by draft floats. The skill's `\figplaceholder` macro serves the same purpose.

## 5. Where floats sit inside chapter files

- Floats are written inline in the chapter file of the section that cites them. No separate `figures.tex`/`tables/*.tex`, no nested `\input`. **[all]**
- Same subsection as the first `\ref`: nearly every referenced main-text float. **[all]**
- Next to the citing paragraph: most floats sit within two paragraphs of it **[most]**.
- Before or after the paragraph? Not uniform: roughly half and half. Some papers put every float just *before* the paragraph that cites it; others put it just *after* the citing paragraph or `itemize`, at the end of the subsection and before the next `\subsection`. With `[t]` floats both land on the next page top, so the rule is proximity, not side: place the float immediately before the paragraph that first cites it, or directly after it when that paragraph ends the subsection.
- The teaser is the first thing after `\section{Introduction}` **[most]**; placing it after P2-P3 also occurs [some]. A page-1 results figure is the first line of `1_introduction.tex`, before `\section{Introduction}` [some; `figures_tables.md` §6].
- `wrapfigure` must open directly before a paragraph's first line [all where used] [venue: NeurIPS/ICLR only].
- Main-table size knob defined right above the table in the chapter file: `\newcommand{\MainTableScale}{0.93}` [some]; the skill's `\shrinktofit` replaces it.

## 6. Where macros live

- **[venue: ICLR]**: everything personal goes into the template's `math_commands.tex`. The personal block is appended after the Goodfellow notation, which ends at `\let\ab\allowbreak`, and packages the port needed are added at the top with a `%` note saying they were added for the port. The main `.tex` keeps the style line, `hyperref`, a few packages and at most one macro (e.g. `\ignore`).
- **[venue: NeurIPS / ICML]**: all packages, colours, macros and boxes in the main `.tex` preamble, between the style line and `\title` (typically 30-50 `\newcommand`s).
- House papers have no separate `macros.tex` or `preamble.tex` [all]. The skill's `templates/preamble.tex` is a deliberate consolidation [skill choice]. When porting to ICLR, the house move is to paste that block into `math_commands.tex`; either works.
- Chapter files define nothing global [all]. Allowed local settings: `\renewcommand{\arraystretch}{0.9}` inside the table [most] and the table-scale knob above.
- The personal preamble is cumulative: macros and boxes from earlier projects stay defined, and a commented line pair such as `% \definecolor{+}{rgb}{0.89,0.09,0.05}` / `\definecolor{+}{rgb}{0.70,0.13,0.13}` travels from project to project. This explains unused macros; it is not a rule to copy.

## 7. Revision trail

### 7.1 Nothing is deleted **[all]**

What stays commented in the compiled files (whole-line `%` comments), by kind: old prose (sentences, paragraphs, bullets); dropped float/table code; layout toggles (`% \vspace`, `% \clearpage`); old headings (`% \subsection{...}`); planning notes.

- Sentence-level: the old sentence stays in place in the one-sentence-per-line flow, next to its replacement (above it, below it, or between its neighbours). Stacks of 2+ alternatives are common [most].
- Paragraph-level: the old paragraph is a commented block directly above the new one [some] or below it [some].
- Inline: `\ignore{old}new` (`\ignore` defined as `{}`) [some].
- Rule for the skill: keep the replaced sentence as a `%` line adjacent to its replacement (above it by default); keep a rewritten paragraph as a commented block above the new one.

### 7.2 Co-author and status macros

- Defined in every project [all]: one macro per person, named after that person's initials. The templates use `\coA{}`, `\coB{}`, `\coC{}`: rename them to each co-author's initials. The list only grows from project to project; status macros (`\concept`, `\revise` or `\rebuttal`, `\rebuttalcaption`, `\warning`, `\draft`, `\possible`, `\revision`) join over time.
- Drafting colours, from the commented coloured blocks: first author orange, other co-authors purple or blue, `\concept` orange (one colour for every concept), `\revision` blue, `\rebuttal` blue, `\warning` red, `\ablation` gray; inactive people stay black.
- Person wrappers left in the final source [most]. Senior-author rewrites are wrapped in that person's macro (e.g. a rewritten Preliminary paragraph, or the example inputs of the introduction).
- `\concept{}` marks key terms, mostly in the introduction (full rules, budget and the `% concept:` table: `concepts.md`) **[most]**, e.g. `\concept{difficulty scorer}`.
- `\revision{}` marks an internally rewritten sentence or paragraph (block or sentence form) [some]. `\possible{}` and `\draft{}` are defined but unused in the final text [most].
- Camera-ready: every macro is **redefined to `{\color{black}{#1}}`; the wrappers stay in the text** [all]. Keeping the coloured set commented directly above the black set makes switching back a comment toggle [some]. The template's `\ifdraftcolors` switch is equivalent.
- Intentionally coloured in the final: `\ablation{}` gray for the ablation row and `\yes`/`\no` teal/red [most]; `\warning{}` red for a content warning under the abstract, only when the paper shows offensive or unsafe text [some].
- Leftover coloured definitions [fix, harmless because unused]: a person macro still defined as red or blue in the submitted preamble. Before submission grep the preamble for `\color{` inside these macros.

### 7.3 Rebuttal-period changes **[venue: ICLR]**

- ICLR papers wrap every discussion-period change in one macro so the revised PDF shows it in colour: `\revise{}` or `\rebuttal{}`, plus `\rebuttalcaption{}` for captions. NeurIPS/ICML rebuttals are text-only, so no macro is needed there.
- What gets wrapped: new appendix sections and subsections (e.g. a computational-cost section with its algorithm, or a connection to a neighbouring field), new baselines and datasets, a rewritten Limitations block with the old sentences commented inside, softening words inside a bold claim (`\rebuttal{in most settings}`), a caption (`\caption{\rebuttal{...}}`). The main text changes by a few words or one pointer sentence; the bulk goes to the appendix [all where used].
- Form: inline `\rebuttal{word}` for small edits; block form with the brace on its own line (`\revise{` ... `}`) around whole paragraphs, subsections, even floats (`\newcommand` is `\long`, so blank lines inside are fine). Captions: `\rebuttalcaption{}` (`\captionsetup` colours label and text) or `\caption{\rebuttal{...}}`.
- Camera-ready: redefine as black, keep the wrappers; keep the blue version commented above [some].
- Venue-port marker: a macro such as `\port{}` wraps text added when moving a paper to another venue, sometimes including a whole section header [some].

### 7.4 Old values in the preamble **[most]**

Colour tuning keeps each old value commented directly above the live one: `% \definecolor{good}{RGB}{0,128,0}` (sometimes several tries) then `\definecolor{good}{RGB}{58,113,104}`. This is the one place where "old above new" is consistent.

### 7.5 Commented alternatives at file level

- Titles: rejected candidates as `% \title{...}` (ICML: `% \icmltitle{...}`) right above the live title, plus running-title variants [some]. Keep only this paper's candidates: a main file copied from another project may still carry that project's titles, so prune them.
- Code link: the anonymous link stays commented above the camera-ready GitHub link [most].
- Chapter versions: a rewrite is a new file (`*_new.tex`; `*_<initials>.tex` for a senior author's rewrite), switched by commenting the old `\input` line above the new one [some]. The Related Works `\input` is commented when it moves to the appendix [most; `structure.md` §3].
- Reference text parked in comments (a baseline paper's abstract, the template's Lorem ipsum) [some]; prune it before release.

## 8. Bibliography file

- **File name = the main file's stem, i.e. the venue template's default name** **[all]**: `iclr2027_conference.bib`, `neurips_2026.bib`, `example_paper.bib` (ICML's template name); `\bibliography{<stem>}`. The skill's templates keep `references.bib` / `main_skeleton*.tex` [skill choice]: rename both to the venue stem when you create the paper (`iclr2027_conference.tex/.bib`, `example_paper.tex/.bib`) and update `\bibliography{}`.
- **Entries are DBLP exports, trimmed** **[all]**: fields almost only `author`, `title`, `booktitle`/`journal`, `volume`, `year` (`doi`, `url`, `biburl`, `timestamp` stripped: at most a handful of `url` fields per file); venue as the DBLP acronym (`booktitle = {NeurIPS}`, `{{ICLR}}`, `{ICML}`, `{ACL}`, `{CVPR}`); arXiv papers as `journal = {CoRR}, volume = {abs/xxxx.xxxxx}` (the dominant form), a minority Google-Scholar style `arXiv preprint arXiv:...`. Aligned `field = {...}` columns as DBLP prints them.
- **Keys renamed to short names** **[all]**: the vast majority of keys; the leftovers are Google-Scholar keys (e.g. `smith2024scaling`). Hyphens join words (`deep-double-descent`, `lottery-ticket`); underscores are rare.
- **Grouped by topic with `%` headers** **[all]**: `% Data selection`, `%% Calibration`, or `%%% Introduction` / `%% <topic>` grouped by the section that cites them; an `% OUTLINE` list of topics at the top [some].
- The `.bib` is carried from paper to paper, so uncited entries are common [most]. BibTeX ignores them, so this is harmless, but it hides slips [fix]: a key defined twice, or `\cite{resnet}` while the entry is `ResNet` (BibTeX case mismatch).
- `\bibliographystyle` order follows the template: NeurIPS `unsrtnat` before `\bibliography`; ICLR/ICML after [venue].

### Bibliography conversion (tool)
`scripts/bib_to_dblp.py IN.bib OUT.bib` rewrites a verified bib into the DBLP-condensed style described above: short venue names (`{ICML}`, `{ICLR}`, `NeurIPS`, `{EMNLP} (Findings)`, `Trans. Mach. Learn. Res.`), `journal = {CoRR}, volume = {abs/ID}` for arXiv-only papers, full author lists (truncated "and others" lists are completed from the arXiv API, capped at 50 names), "First Last" names with LaTeX accent escapes, and acronym-only title protection because the venue .bst lower-cases unbraced title words. Keys are kept; conference entries keep only `author`, `title`, `booktitle`, `year`, journal entries also `volume`, `number`, `pages` (TMLR: `volume` = year when absent), and `@misc` entries keep `note`. DBLP itself blocks scripted access (bot check), so the script works from verified metadata; never bypass the check. An unmapped venue stops the run before any network request: add its full name and short form to the script's venue tables. Name the output after the main file (`example_paper.bib`, `iclr2027_conference.bib`), as §8 requires [all]. Standard library only; the network is used only for the arXiv author lookups.

## 9. Lines and paragraphs in the `.tex`

- One sentence per line [all], and **no hard wrapping**: 95-99% of main-text prose lines end at a sentence end, colon/semicolon or closing brace. Median line length 150-194 characters; p90 260-370. Never wrap at 80 columns.
- Enumerations stay on separate lines inside the sentence: `(i) ... ;` / `and (ii) ...` split over two lines in abstracts and introductions [some], each line still a clause.
- Paragraphs are separated by one blank line; median 2-3 source lines (sentences) per block in the main text; no `\par`, no `\noindent`, no `\\` in prose [all]. Bold run-in heads open the line: `\textbf{Baselines.} We compare ...` [all].
- Each chapter file opens with its `\section{...} \label{...}` (or `\begin{abstract}`, `\appendix`), optionally preceded by commented outline lines **[all]**; never by a negative `\vspace` (`venues.md` §1.2).

## 10. Comment density

- Whole-line `%` comments as a share of non-blank lines: **15-20% in the main text while drafting [most]; the appendix about 10% [most].**
- Trailing comments after code are rare (≤2% of lines), mostly option notes inside floats.
- Comments pile up where the text was rewritten most: abstracts, conclusions and experiments, where 25-55% of the lines can be comments.
- Planning notes **[most]** hold (a) logic outlines at the top of a chapter, (b) a rough draft of the target paragraph, (c) reviewer-strategy notes (e.g. why the scope is narrowed so that reviewers do not ask for many more baselines), (d) LaTeX mechanics notes, (e) to-do flags. See also `structure.md` §3 and SKILL Step 1.
- Release hygiene [fix]: the compiled PDF ignores comments, but arXiv source uploads expose them. Before an arXiv upload, strip comments (`arxiv_latex_cleaner` or `latexpand --empty-comments`) rather than editing the working source.

## 11. `\input` and file layout

- `\input` only; `\include`/`\includeonly` never used **[all]**.
- `\input{chapters/N_name}` without extension, path from the project root; only the ICLR template line keeps one (`\input{math_commands.tex}`) **[all]**.
- Flat: every `\input` is in the main file, chapter files never `\input` further files [all].
- Chapter file names `N_name.tex`, lowercase, underscore after the digit [all]. Rename the file when its content changes [fix]: a chapter file whose name no longer matches its content is a slip. The skill's ICML template names the file that holds every appendix section `chapters_icml/8_appendix.tex`.
- Statements live in the main file (ICML Impact Statement and Acknowledgements before `\bibliography`; NeurIPS `ack`) or in their own chapter file (ICLR `8_ethics_reproducibility_statement.tex`) [venue].

## 12. Convention strength summary

| Convention | Strength |
|---|---|
| Labels `<type>:<lowercase-hyphenated>`; `sec:` main, `apdx:` appendix; `\label` on the heading line, after `\caption`, on `\begin{equation}` | [most] (hyphens, `apdx:`), [all] (placement) |
| Appendix figures marked `apdx` | [most] |
| Stock labels (`fig:teaser-figure`, `fig:main-figure`, `tab:main-table`, `apdx:case-study`, ...) | [some] each |
| Panels `-a/-b` | [some] |
| Space before `\ref` (recommend `~`) | [all] (space); `~` is the skill's [fix] |
| `figures/<topic>/<content>_<model>.png` from the root, with extension, no `\graphicspath`; stale files kept | [all] |
| `figures/Placeholder.png` | [most] |
| Float inline, same subsection as first `\ref`, next to the citing paragraph | [all] |
| Teaser right after `\section{Introduction}` | [most] |
| Macros: ICLR in `math_commands.tex`, NeurIPS/ICML in main preamble; none in chapters | [venue: ICLR] / [venue: NeurIPS, ICML]; [all] (none in chapters) |
| Old wording kept as adjacent `%` lines; dropped floats and headings commented | [all] |
| Old colour value commented above the live one | [most] |
| Person macros by initials, neutralised to black, wrappers kept | [all] |
| `\concept{}` for coined terms | [most] |
| Rebuttal changes in `\rebuttal{}`/`\revise{}` | [venue: ICLR] |
| Rejected titles commented above `\title` | [some] |
| `.bib` named like the main file; DBLP entries with acronym venues; `%` topic headers | [all] |
| One sentence per line, no hard wrap, blank line between paragraphs, no `\\`/`\par`/`\noindent` in prose | [all] |
| Comments 15-20% of main-text lines; planning notes as comments | [most]; [most] |
| `\ignore{}`, `\iclr{}`, table-scale knob, parked reference abstract | [some] |
