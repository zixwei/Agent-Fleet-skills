# Experiments: skeleton, RQs, setup, analysis paragraphs, tables, plans without data

Evidence labels follow the legend in `SKILL.md`: `[all]`, `[most]`, `[some]`, `[current]`, `[older]`, `[venue: X]`, `[fix]`, `[skill choice]`.

All examples are invented. The running method is DIVA, a data-filtering loop with a difficulty scorer and a diversity scorer (see `paper_types.md`). Other placeholders: `X`, `<Prior>`, `Model-S` / `Model-L`, `Bench-1..6`, `Set-A/B/C`, `Baseline-A/B/C`.

## 1. Size and placement

- A method paper has ONE `\section{Experiments}` after the Methodology [all]. Findings papers spread results over claim-titled sections [some].
- Main text: ~850-1,150 prose words and ~2.6-3.0 pp including floats [most]. The ICLR 9-pp blueprint gives 850-1,000 words.
- Floats: 1-2 result tables + 2-6 result figures [most].
- Show mechanism and ablation evidence as a training-dynamics curve or a bar chart rather than a table [most].

## 2. Section skeleton

```latex
\section{Experiments} \label{sec:experiments}
In this section, we examine X through the research questions below;
Appendix~\ref{apdx:experiments} adds further analysis (\eg <x>, <y>, <z>).
\begin{itemize}[leftmargin=*]
  \item \textbf{RQ1:} (Performance) Does X <goal> better than <baseline family> while <keeping cost/property>?
  \item \textbf{RQ2:} (Mechanism) How does <component> change <behavior>? How does <quantity> evolve during training?
  \item \textbf{RQ3:} (Ablation Study) How sensitive is X to the hyperparameter $\alpha$? What does each component contribute?
  \item \textbf{RQ4:} (Case Study / Extension) Can X serve as <extra use>?
\end{itemize}
\subsection{Experiment Setup} \label{sec:exp-setup}   % [current]; otherwise the run-ins sit directly here [some]
\subsection{(RQ1) <Noun Phrase>} \label{sec:main-performance}
\subsection{(RQ2) <Mechanism of X>} \label{sec:mechanism}
\subsection{(RQ3) Ablation Study} \label{sec:ablation}
```
Rules:
- **Opener.** Name the method and announce the RQ list in one sentence: "In this section, we examine X through the research questions below:" [some]. Alternatives are a bare "Our experiments address four questions:" [some] or going straight to the list [some]. Then give the RQ bullets.
  - An appendix pointer in the opener is optional [some]: "..., and further analysis (\eg scaling to larger backbones, per-component studies, and sensitivity to $\tau$) is in Appendix~\ref{apdx:experiments}."
- **RQ bullets.** Write `\item \textbf{RQk:}` + a yes/no or "How does ..." question that names the method [all].
  - Parenthetical topic labels are optional [some]: "(Performance)", "(Performance and Cost)", "(Scorer Behavior)", "(Ablation Study)", "(Case Study)".
  - One bullet may hold two questions [some].
- **Number.** Use 3 RQs [some] or 4 [some]. Never leave a dangling RQ [fix]: a bullet commented out while the others kept their numbers leaves a gap in the numbering.
- **Order.** Performance first, then mechanism, then ablation/sensitivity, then case study or extra use. A feasibility check that isolates the new signal may come before performance, e.g. "(RQ1) Reliability of the Difficulty Scores" [some].
- **Setup.** Two forms are in style:
  - `\subsection{Experiment Setup}` with a one-line opener: "We summarise the setup below; Appendix~\ref{apdx:impl-details} gives the full details." [some] [current]. The subsection enables "(\cf Section~\ref{sec:exp-setup})".
  - The run-in paragraphs directly under `\section`, after the RQ list [some].
- **Subsection titles.** Prefer the `(RQk)` prefix + a Title-Case noun phrase [some] [current]. The suffix form "<Noun Phrase> (RQk)" is also used [some]. Use one form per paper. Each subsection gets a `\label{sec:...}` that intro P5 forward-references.
- **Intro P5.** It has exactly one sentence per RQ subsection, in the same order, each ending with `(\cf Section~\ref{...})` [current]. Write P5 after the RQ list is fixed.

## 3. Setup paragraphs

Order: models → data (training + evaluation) → baselines (only if there is a Baselines paragraph) → metrics. Metrics are either inlined in the data paragraph [some] or defined at the start of RQ1 [some]. Each paragraph has 1-5 sentences and ends with an appendix pointer.

Run-in head sets in use (one set per paper):

| Pattern | Setup heads, in order | Evidence |
|---|---|---|
| Models + one data paragraph | `\textbf{LLMs.}` `\textbf{Training and evaluation.}` (no Baselines paragraph) | [some] [current] |
| Models + benchmarks | `\textbf{LLMs.}` `\textbf{Evaluation Benchmarks.}` (no Baselines paragraph) | [some] |
| Models + one head per benchmark family + baselines | `\textbf{LLMs.}` `\textbf{<Family-1> benchmarks.}` `\textbf{<Family-2> benchmarks.}` `\textbf{Baselines.}` | [some] |

- **`\textbf{LLMs.}`** (or `\textbf{Backbones.}` / `\textbf{Models.}`, named after what is trained):
  - Example: "We run all experiments on two open backbones of different scale, Model-S and Model-L \cite{T5}."
  - Always use ≥2 sizes or families [all].
  - Justify any preprocessing: "Before training, we remove exact duplicates from Set-A and drop inputs shorter than <n> tokens, since duplicates would inflate the in-domain scores." [some]
- **`\textbf{Training and evaluation.}`** [some] / `\textbf{Evaluation Benchmarks.}` [some] / `\textbf{Datasets.}` [some]:
  - Training data first: "We train on Set-A \cite{ImageNet}, which holds <n>k labelled examples." [some]
  - Group benchmarks by aspect: "For in-domain accuracy, we evaluate on Bench-1 \cite{}, Bench-2 \cite{}, and Bench-3 \cite{}. For robustness to distribution shift, we use Bench-4 \cite{} and Bench-5 \cite{}." [some]
  - Mark in- vs out-of-distribution (optional example): "Only Bench-1 shares the training distribution; we treat the other four as out-of-distribution." [some]
  - Follow a named protocol (optional example): "We adopt the evaluation protocol of <Prior> \cite{} without changes." [some]
  - Explain subsampling: "Because Bench-5 is large, we evaluate on three random subsets of <n> examples each ..." [some]
  - Give the evaluation protocol + metric in two sentences: "We evaluate the final checkpoint of each run on the full test split, with three random seeds per setting. We report mean top-1 accuracy over the seeds." [some] For classic metrics, state the default K: "... top-$K$ accuracy, with $K$=5 unless stated otherwise." [some]
  - Add one training-budget sentence: "Every training run uses the same budget of <n> optimisation steps." [some]
- **`\textbf{Baselines.}`** paragraph when there are ≥2 baseline families [some]. A single natural baseline is instead named inside the performance RQ, with the shared starting checkpoint [some] (third bullet below):
  - Group by paradigm (≥3 methods from ≥2 families), mirroring the Preliminary / literature-review taxonomy: "We compare against four filtering methods from two families: three score-based filters, Baseline-A \citep{}, Baseline-B \citep{} and Baseline-C \citep{}, and one clustering-based filter, Baseline-D \citep{}." [some] Other taxonomies split heuristic from learned, or data-side from loss-side.
  - Add an ablation baseline and a method from a related family: "We further include X's difficulty scorer used alone as an ablation baseline (Diff-only), and one method from a neighbouring family, <Prior> \citep{}." [some]
  - Introduce a single natural baseline inside the performance RQ, with the shared starting point: "We compare X with standard <Prior>, which trains on the unfiltered pool, in Table~\ref{tab:main-table}. Both methods start from the same fine-tuned checkpoint." [some] Alternatively, name the baseline in the RQ1 question and state that both methods see the same training data [some].
  - Fairness notes: "All methods share the backbone and the training budget, so differences come from the filtering rule alone." [some]
  - End with pointers: "Appendix~\ref{apdx:baselines} describes each baseline in detail." [some]
- **`\textbf{Statistical significance.}`** [venue: NeurIPS]. It answers the checklist item "Experiment statistical significance". No ICLR/ICML checklist item asks for it; omit it there. Example: "Over all model sizes and benchmark columns of Table~\ref{tab:main-table}, we test X against <Prior> with paired two-sided tests; the gains are significant at $p < 0.05$." A p-value in the caption is an alternative [some].

## 4. Implementation details (appendix; the main text keeps one line)

The main text keeps:
- model names and benchmark names;
- grouped baselines with citations;
- evaluation protocol + metric in one sentence, and the training budget in one sentence;
- the main table and the key figures.

The appendix `Implementation Details` (`apdx:impl-details`) holds the rest [most], in this order:
1. Frameworks, with footnote URLs: "We implement training with <framework-1>\footnote{<url>} and evaluation with <framework-2>\footnote{<url>}."
2. Hardware ("on a single <GPU type>"; "<n> <GPU type> GPUs, about <m> GPU days in total"), or a `\textbf{Compute disclosure.}` paragraph [venue: NeurIPS]. That paragraph answers checklist Q8, as the licence table answers Q12.
3. Stage recipes as bold run-ins (`\textbf{Warm-up.}`, `\textbf{Main training.}`).
   - State the hyperparameters in prose: "max input length = <L_in>, max output length = <L_out>, batch size = <B>, ..., learning rate = <lr>".
   - Repeat them in a `tabularx` table [some].
   - Explain non-obvious settings.
4. Method hyperparameters with per-model values [some]: "(1) the keep ratio $\rho$, set to <v>; (2) the regularisation weight $\alpha$, set to <v>; and (3) the temperature $\tau$, set to <v_S> and <v_L> for Model-S and Model-L."
5. Data construction with counts, splits and a leakage check: "To avoid test leakage, we drop every training or validation example that has a near-duplicate (<similarity> above <threshold>) in any test set." [some]
6. The judge model and its prompts, for model-judged metrics [some].

After these come:
- Benchmarks: one bullet per dataset, `\item \textbf{Name} \cite{} ...`.
- Baselines: one bullet each with a 1-2 sentence mechanism. Say why some families are excluded [some].
- Prompts (tcolorbox figures), Additional Experiments, Analysis, Computational Cost, Case Study.

## 5. Analysis paragraphs

**Lead-in**: what is reported where, how to read it, then "We have the following observations:" [most]. The [older] form is "We observe that:".
- "Table~\ref{tab:main-table} reports the average accuracy and filtering time over all budgets, and Figure~\ref{fig:budget-breakdown} breaks both down per budget; per-benchmark results are in Appendix~\ref{apdx:experiments}. We have the following observations:"
- Reading a curve: "..., where thick lines show a rolling mean over 3 evaluations and thin, light lines show the raw values." [some]

**Finding format** [all]: a one-sentence bold claim, written as a full declarative sentence with the period inside the bold.
- Main results: `\begin{itemize}[leftmargin=*]` with 2-3 `\item \textbf{<claim>.}` [most].
- Mechanism/ablation: bold-claim paragraphs without bullets [some].
- Typically ≤2 findings per RQ [some]. The main-results RQ may have 3 [some].

**Body of a finding.** The core [all] is:
1. the bold full-sentence claim;
2. the evidence: "As shown in Table~\ref{...}, at both model sizes and under every budget, X beats <Prior> on five of six benchmarks.";
3. one interpretive sentence.

Optional moves, in this order when used (all five together in [some]):
- Strengthening: "Moreover, ..." / "Additionally, the margin widens with the larger backbone (\ie ...)." / "For example, ..."
- Attribution [most]: "We attribute this gain to ..." / "This phenomenon is because ..."
- Contrast [most]: "In contrast, the baselines vary widely from one benchmark to the next .... A likely reason is their fixed scoring rules, which do not adapt to ...."
- Closer: "These results ..." [most] or "Such results underline the value of ...." [some]. Add "consistent with Theorem~\ref{}" when there is theory [some].

**Finding order in RQ1**:
1. About the paradigm: "Filtering the training pool improves sample efficiency across backbones."
2. About the design space [some].
3. About ours: "X consistently outperforms ...".

**Numbers in prose**: a relative range ("gains from 1.2 to 3.4 points on Bench-2"), a peak ("up to <k>×"), or an average ("an average accuracy above 80\%"). Never paste the table into prose.

**Mechanism RQ** [some]: one bold run-in head per component or aspect (`\textbf{Difficulty scorer.}` / `\textbf{Diversity scorer.}`; `\textbf{Effect on accuracy.}` / `\textbf{Effect on the kept data.}`). Each paragraph runs:
1. what we study: "We examine how the diversity scorer keeps near-duplicates from dominating a batch by tracking ...";
2. the figure reference;
3. the mechanism: "This is because ...";
4. the consequence: "As a result, ...".

Constructed stress settings are welcome: "To test this, we build a skewed training pool in which one source contributes most of the examples." [some]

**Ablation RQ**:
- A setup sentence with the condition: "To isolate its effect, we retrain Model-L with the diversity scorer removed ..."
- Name the variants `<Method> (w/o <component>)`: "DIVA (w/o diversity)", "w/o scorer" [some].
- Optional: a control that randomises the new signal, e.g. "DIVA (Random scores)", "<Prior>+Rand" [some].
- Optional: a sweep of the key coefficient that ends by justifying the default: "Given this trade-off, we use $\tau$ = 0.7 by default, since ..." [some]. Tie a trade-off revealed by the sweep to the Limitations [some].
- Baseline-heavy papers may put the ablation inside the main table as a gray row "+ X (w/o C1) (Ablation)" [some].

**Case study RQ** [some]: one short paragraph narrating a tcolorbox figure, ending "Appendix~\ref{apdx:case-study} shows more cases." Other papers keep case studies in the appendix only, to save space [some].

**Point to the appendix wherever material moved there** [all]. A pointer in every RQ subsection is an option, not a rule: [some] papers do this, others point only once or twice.

**RQ subsection frame (cohesion, `prose.md` §4b rules 5-6).** Open each RQ subsection after RQ1 with a bridge from the previous one: a purpose infinitive ("To examine whether the diversity scorer matters on its own, we ..."), a basis clause ("Given that the learned scores are reliable, we further test ...") or "Finally, we test whether ...". "In this section, we study ..." may follow the bridge but does not replace it. End the subsection with its conclusion ("Such results show ...", "This indicates ..."), with the appendix pointer placed before that sentence or folded into it ("..., with more results in Appendix~\ref{}"); a bare appendix pointer is never the last sentence of an RQ subsection. When cutting for pages, keep the clause that says which part of the RQ a result answers ("which answers the cost side of RQ2").

## 6. Laying out the result tables (summary; full rules in `figures_tables.md` §7)

- Use `table*` for the main table even in one-column venues. Put the caption ABOVE, the `\label` after the tabular, and `\vspace{-16pt}` at the end [current].
- Pick the variant first (`figures_tables.md` §7, "Main-table variants"). Each variant is [some]:
  - T1 scale-grouped: no Avg, shaded row without "(Ours)";
  - T2 per backbone: Avg column and bold "+ X (Ours)";
  - T4 paired quality/cost rows.
- Use a two-level header: a bold spanner ("Datasets", "Benchmarks", "Accuracy \% $\uparrow$") over bold column names. Put the unit and arrow in headers when the table mixes directions or metrics [some]. A one-metric table has none [some].
- Rows:
  - `cl|...` with `\multirow` scale groups [some], or `l|...|c` with backbone blocks [some];
  - `\cmidrule(lr){2-N}` separates non-trained references (Base, SFT) from trained methods;
  - `\midrule\midrule` between scales/backbones [current].
- Bold the best only. No underline, stars or percentage-improvement row [current].
- When efficiency is a claim, give each method two metric rows (quality / cost) tinted pink/blue [some], not a separate section.
- Precision: 2 decimals for %-metrics; 3 for scores in [0,1]; no ± std in main tables.
- Templates: `templates/tables.tex` T1-T8.

## 7. Writing an experiment plan with EMPTY result tables (no data yet)

The house habit is to draft experiments before results exist [some]:
- the full row/column structure with blank cells;
- `\cellcolor{qualityrowcolor}\strut` placeholder cells and empty captions;
- filled in once results arrive.

Reproduce that, but never invent a number.

Procedure:
1. **Derive RQs from the claims.** Write one RQ per claim in intro P4/P5. Order them performance → mechanism → ablation → extension (§2). Each RQ becomes one `(RQk)` subsection with a label.
2. **Write the full Experiment Setup now.**
   - Models (≥2 sizes), training data, benchmarks grouped by aspect with ID/OOD marked, the evaluation-protocol and metric sentence, the training budget.
   - Baselines: with ≥2 families, a Baselines paragraph (grouped, with the ablation baseline). Otherwise, name the single baseline and the shared starting checkpoint in the RQ1 lead-in.
   - Unknown values become `<...>` placeholders, not guesses.
3. **Build every table at final shape** from `templates/tables.tex`.
   - Include all planned rows (Base, SFT, each baseline, the ablation row, `+ X`) × all benchmark columns (+ Avg. only in the T2 variant).
   - Fill cells with `\pend`, which renders "--" and keeps column widths stable.
   - The caption states the final bolding rule. Do not bold any cell yet.
4. **Figures** become `\figplaceholder[<height>]{<what the plot will show: x-axis, y-axis, series>}` inside the final `figure`/`subfigure`/`wrapfigure` geometry. Give each the final caption's title phrase and an empty takeaway slot.
5. **Write findings as falsifiable expectations.**
   - Keep the observation block, but mark each bold claim as expected: `\textbf{\draft{[Expected]} X generally outperforms <baseline> across diverse benchmarks.}`
   - Follow it with the prediction, in the falsifiable-prediction transition of `prose.md` §4: "If <mechanism> holds, then <measurable pattern> is expected." [some]
   - Add what would falsify it.
   - When the data arrive, remove every `[Expected]` marker and rewrite the claims to match the numbers (soften overclaims per `prose.md` §9 rule 7).
   - **Prediction block, fixed in advance** (not "pre-registered", which means a time-stamped protocol; `concepts.md` §3) `[skill choice]` (one template for every RQ, so the RQs read alike; use it when the paper fixes its predictions before any run):
     ```latex
     Based on Proposition~\ref{prop:x} (Section~\ref{sec:analysis}), we fix the following predictions before any run:
     \begin{itemize}[leftmargin=*]
       \item \textbf{\draft{[Expected]} DIVA keeps more rare clusters than one-pass filtering at the same budget.}
       If the quota on each cluster works as intended, then the kept set should cover more clusters on every benchmark.
       The prediction is rejected if coverage is equal or lower on most benchmarks.
     \end{itemize}
     ```
     Order: an introduction sentence naming the source of the prediction (proposition or section) → bold prediction → "If ..., then ... should ..." → "The prediction is rejected if ...". Every prediction is bold. At most 2 "is expected to" per section; otherwise "should", "may", "is likely to" (`prose.md` habit 12). The introduction sentence also keeps the block from starting with an unlinked sentence.
6. **Per-RQ plan block**, as `%` comments above each subsection or as a plan table in an appendix draft:

| RQ | Claim tested | Setting (models × data) | Metric | Table/Figure | Expected pattern | Falsified if |
|---|---|---|---|---|---|---|
| RQ1 | X beats <baseline> at lower cost | Model-S/L × Bench-1..6 | accuracy, time | Tab. 1 (T1/T4) | higher on most benchmarks at both sizes | no gain at either size |
| RQ2 | <component> causes <effect> | Model-L, stress setting | <internal quantity> | Fig. k (curve) | slower growth vs baseline | curves overlap |
| RQ3 | each component contributes; α default | Model-L | Avg. | bar chart / T5, T6 | w/o variants below full X | a w/o variant matches X |

7. **Intro P5 and the abstract** may describe the planned evidence only in hedged, qualitative form ("We further run experiments to examine ..."). No numbers until results exist.
8. Before submission, grep for `\pend`, `[Expected]`, `\figplaceholder` and `<` placeholders. None may remain.

## 8. Checklist

- [ ] Opener "In this section, we examine X through ..." [some] + 3-4 RQs, ordered performance → mechanism → ablation → extension. The appendix pointer in the opener and the topic labels are optional [some].
- [ ] Setup run-ins, under `\subsection{Experiment Setup}` [current] or directly under the section [some]:
  - `\textbf{LLMs.}` (≥2 sizes);
  - a data paragraph (aspects, ID/OOD, evaluation protocol + metric, budget).
- [ ] Baselines:
  - `\textbf{Baselines.}` only with ≥2 baseline families, grouped by the RW taxonomy, plus an ablation baseline [some];
  - otherwise, name the single baseline in the performance RQ with the shared starting checkpoint [some].
- [ ] `(RQk) Title` subsections with labels matching intro P5.
- [ ] Observation blocks:
  - "We have the following observations:" [most];
  - bold claim sentences of 8-20 words + evidence + one interpretive sentence [all];
  - attribution, contrast and a "These/Such results ..." closer as needed;
  - typically ≤2 findings per RQ (3 for RQ1).
- [ ] Ablation: `X (w/o C)` naming [some]; optional randomised control [some] and a sweep justifying the default [some].
- [ ] An appendix pointer wherever material moved. Implementation details, benchmark and baseline bullets, prompts and extra results go in the appendix.
- [ ] No-data mode: `\pend` cells, placeholder figures, `[Expected]` claims with falsification criteria. Remove all of them before submission.
- [ ] RQ subsections after RQ1 open with a bridge (purpose infinitive / "Given ..., we further test ..." / "Finally, ...") and end with a conclusion sentence, not with a bare appendix pointer (`prose.md` §4b).
- [ ] Experiments analysis: ≤ 5% of sentences without a link, connective-led share ≥ 35% (`check_paper.py` `== cohesion`); setup paragraphs use "For the X, ..." / parallel frames / "Additionally," (`prose.md` §4b rule 10).
- [ ] Predictions fixed in advance (if any) use the one block template of §7 step 5 in every RQ.
