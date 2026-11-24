# Math: notation, macros, equations, theorems, proofs, algorithm blocks

Evidence labels follow the legend in `SKILL.md`: `[all]`, `[most]`, `[some]`, `[current]`, `[older]`, `[venue: X]`, `[fix]`, `[skill choice]`. Formal results appear only in `[some]` papers of the house style. Their conventions are binding whenever the new paper has a formal result. The rules marked "theory-only" below apply in that case.

Running examples are invented (see `paper_types.md`). DIVA is a data-filtering loop with a difficulty scorer and a diversity scorer. PriorTilt is a label-shift correction solved in closed form under a divergence budget. CaliLoss is a calibration loss with a bound. SynthBonus is a shaped RL bonus built from an ensemble of cheap estimators.

## 1. Notation

| Object | Rule | Evidence |
|---|---|---|
| Sets, datasets, text objects (input, target, generated output, batch, state, reference set) | calligraphic: $\mathcal{X}, \mathcal{Y}, \mathcal{B}_k, \mathcal{S}_k, \mathcal{D}_{\text{train}}$ | [all] |
| Training objective | $\mathcal{J}(\theta)$ for RL; `\Lapl_{\text{Name}}` for a loss (bold calligraphic L) | [some] (RL papers); [some] (loss papers) |
| Model-generated / predicted / estimated | hat: $\hat{\mathcal{Y}}$, $\hat{\mathcal{S}}_k$, $\hat{\mathbf{W}}$, $\hat{s}_k$; ground truth never has a hat | [most] |
| Learnable pre-object / optimum / pseudoinverse | tilde $\tilde{\mathbf{W}}$ / star $\boldsymbol{\pi}^{\star}$ / $^{+}$ ($\mathbf{Z}^{+}$) | [some] |
| Vectors and matrices (incl. Greek) | bold upright `\mathbf{}`: $\mathbf{z}$, $\mathbf{W}$, $\mathbf{\Lambda}$; not `\bm` | [most] (`\bm{}` occurs in [some] and is dropped: [skill choice]) |
| Layer / instance / iteration index | parenthesised superscript $\mathbf{z}^{(k)}$; modified quantity with a prime $\mathbf{z}^{(k)'}$; updates with `\leftarrow` | [all] (index); [most] (prime, `\leftarrow`) |
| Spaces, expectation, indicator | $\mathbb{R}^{d\times N}$, $\mathbb{E}$, $\mathbb{I}(\cdot)$ (not `\mathbbm{1}`) | [some] |
| Hyperparameters | Greek, each introduced right after the display with its role: λ strength, α weight, τ temperature, ε clipping range, β KL weight | [all] |
| Labels on rewards / scores / losses | upright `\text{}` superscript for the label, subscripts for indices: $s^{\text{div}}$, $\ell^{\text{div}}_k$, $\hat{s}^{\text{pair}}_{i,k}$ | [some]. Another [current] form puts the role in the superscript and the label as an italic subscript ($s^{A}_{div}$). The skill picks the upright-label form for typographic correctness: [skill choice], not a consensus |
| Role tags | superscript; colour it only if the figures use the same colour: `f_\omega^{\text{\textcolor{roleB}{$S$}}}` | [some] |
| Definition symbol (optional) | `\triangleq` | [some] (the others write plain `=`) |
| Code-like functions | typewriter: `\texttt{match}(\mathcal{Y},\hat{\mathcal{Y}})`, `\mathtt{route}(\hat{\mathcal{G}})`, `\texttt{True}`; standard operators via `\operatorname{diag}` | [some] |
| Dimensions | declared inline at first use: "$\mathbf{P} \in \mathbb{R}^{N \times C}$ stacks the $N$ predicted class distributions ..." | [some] |

- Say so when you simplify notation: "To keep notation light, we drop the depth superscript $^{(k)}$ from here on." Or: "We reuse $s(\cdot)$ for the batch-level score, a mild abuse of notation." [some]
- Use one symbol per concept, define every symbol before use, and keep labels unique. [fix] Published drafts slip here: an equation label defined twice, two hyperparameters swapped between the prose and the display, or a label left in an older subscript form after the notation changed. Be stricter than common practice.
- The inline formula in intro P4 must use exactly the Methodology symbols [some].

## 2. Macro kit

`templates/preamble.tex` §B3-B5 holds the macro kit that every source preamble shares [all]:
- `\ie \eg \cf \wrt \etc \etal \aka \st`. All of them define `\ie`/`\eg`/`\cf` as `\emph{i.e., }` and so on [all].
- `\norm \Lapl \Trans \Mat \Set \Space \Bracs \ParaTitle \myeq \eqc`.
- `\argmax \argmin \sign \Tr` (guarded).
- `derivation_out` / `derivation_full` [some].

Theorem environments (§B6) follow what the theory papers actually print. Each theory paper uses ONE scheme, and no paper mixes the two (each scheme is [some]):
- A paper with only definitions and (borrowed) lemmas uses plain counters.
- A paper with a headline theorem uses within-section numbering for all formal environments, or accepts the mix knowingly.

ICML's kit prefers one counter shared within sections ("Definition 2.1, Proposition 2.2"; `venues.md` §1.6).
- **Definitions and lemmas: plain per-environment counters, no section prefix** [some] [current]. Declare them as `\newtheorem{definition}{Definition}` and `\newtheorem{lemma}{Lemma}` (at ICLR in the paper's `math_commands.tex` [venue: ICLR], elsewhere in the preamble). They print "Definition 1", "Lemma 1".
- **A headline theorem: `restatable`, numbered within the section** [some]. Use `\declaretheorem[name=Theorem,numberwithin=section]{thm}` + `\begin{restatable}{thm}{MainTheorem}`. It prints "Theorem 3.1" and is restated in the appendix with `\MainTheorem*`.
- Provenance:
  - `\declaretheorem{thm}` sits in [most] preambles but only [some] papers use it.
  - A shared-counter set numbered within sections (theorem, proposition, lemma, ...; "Definition 3.1", "Lemma 3.2") appears in [some] preambles only as unused template boilerplate. The skill does not load it.
  - The one reason to switch is the ICML kit's preference (`venues.md` §3.4 says how to replace B6 with it).
  - The source set never uses proposition, corollary, assumption or remark. The template gives them plain counters [skill choice].

Equation references:
- ICLR [venue: ICLR]: the official `math_commands.tex` redefines `\eqref{x}` → "equation~\ref{x}" and `\Eqref{x}` → "Equation~\ref{x}". Write `\Eqref{}`.
- [fix] Writing `\eqref` under that file breaks in two ways:
  - "Equation \eqref{}" renders "Equation equation 1".
  - A parenthetical "(see \eqref{eq:x})" renders a lowercase "(see equation 1)".
- NeurIPS/ICML: "Equation~\ref{}" or "Equation~\eqref{}".

## 3. Equation presentation ([all] unless noted)

1. **Lead-in clause ending with a colon**. Examples: "..., which takes the form:", "... is computed as follows:", "Stacking the $N$ reference vectors gives the matrix form:", "Setting the gradient to zero yields the closed form:". Cite the source of a borrowed formula in the lead-in [some]: "is normalised as \cite{LayerNorm}:".
2. **Punctuation inside the display**: every display ends with "," or "." [most].
3. **"where" clause** in lowercase right after the display. It names EVERY new symbol: "where $\mathbf{P} \in \mathbb{R}^{N \times C}$ stacks the predicted distributions of the new site, and $\lambda$ is the Lagrange multiplier of the budget." [some] The alternative is "Here, $f_\omega$ and $f_{\text{ref}}$ denote ..." [some].
4. **So-what closer**. Examples: "This keeps an example only when both scorers accept it, and drops it otherwise." · "Such a term discourages near-duplicate batches." · "In this way, the corrected prior never leaves the budget set."
5. **One idea per `equation`**. Number every display. Label only the referenced ones, with `\label{eq:kebab-case}` right after `\begin{equation}` (`eq:budget-constraint`, `eq:coverage-term`) [some].
6. **Multi-line**:
   - The main text uses `equation` + `aligned` with one number. Continuation rows start with `\text{where}\quad` / `\text{and}\quad` [some].
   - Appendix derivations use `align` with `&=` and `\notag` on all but the last line [some].
   - Avoid `gather` (it occurs in [some] only).
7. **Piecewise terms** use `cases` with text conditions: `1, & \texttt{match}(\mathcal{Y},\hat{\mathcal{Y}}) \\ 0, & \text{Otherwise.}` [some].
8. **Paired short definitions share one line**, separated by `, \quad` or `\qquad` [some].
9. **One bold run-in paragraph per term, then a "Total ..." closer** [some]. The sequence is `\textbf{Coverage term.}` … `\textbf{Diversity term.}` … `\textbf{Total objective.}`. The closer reads "Finally, the full objective ... sums the two terms with a weight:", then the display, then one "Such an objective ..." sentence.
10. **Optional [some]: `\underbrace{}` with `\text{}` names the two sides of the key identity.** Use it once, on the paper's key equation: `\underbrace{...}_{\text{Expected calibration error}} \le \underbrace{...}_{\text{CaliLoss}}`.
11. **Theorem/lemma references** [some]: "Theorem~\ref{thm:main}", "Lemma~\ref{lemma:gibbs}", "Using Lemma~\ref{...}, we next derive ...".
12. A method subsection typically has 2-3 displays. The whole main text has 6-10 numbered equations [some].
13. **Two-column displays [venue: ICML], checked in a Times rebuild.** The column is 3.25 in.
    - A long objective is one `equation` with `aligned`, broken by hand. Continuation rows start with `\text{where}\quad` / `\text{and}\quad`, and delimiters are sized `\Bigg[ \Big(`. The number may drop below the block.
    - Put one definition per display row. Two definitions on one row forced the equation number onto its own line in Times and overflowed the column by several points in Latin Modern. Stack them in `aligned`.
    - `cases` with short text conditions (3-4 words) fits. Move longer conditions into the prose ("Here, $k_{\text{stop}}$ denotes ...").
    - Never `\resizebox` an equation and never use `\small` math (`check_paper.py` lints scaled math at ICML). If a display cannot be broken below about 3.1 in, move it into a full-width `figure*` (caption or illustration) or into the appendix.

## 4. Formal results (theory-only rules, [some])

Empirical papers have none. At NeurIPS they answer the checklist theory item with NA [venue: NeurIPS]. Do not add a theorem just to look rigorous. When the paper has one:

1. **Motivate before stating.**
   - "Although the regulariser works well in practice, why it improves calibration has not been explained. We analyse it below through proper scoring rules \cite{Survey-Calibration}." (CaliLoss)
   - "However, solving the constrained problem by projected gradient needs many passes over the unlabelled set. We therefore rely on the following lemma:" (PriorTilt)
2. **Borrowed results carry a citation in the optional title**: `\begin{definition}[Kullback-Leibler Divergence \citep{Info-Theory}]`, `\begin{lemma}[Gibbs Variational Principle \citep{Info-Theory}]`. Own results get a descriptive title or none ("Theorem 3.1").
3. **Self-contained statement.** Define everything inside the statement: "Let $\hat{p}$ be the predicted confidence and define $\Omega_h(\hat{p}) \triangleq \ldots$. Then $\Lapl_{\text{Cali}}$ \textbf{upper-bounds} the expected calibration error up to an additive $O(h)$ term ...". Bold the key relation. Keep short math inline and use a display only for the headline relation.
4. **No Assumption environment** in the theory papers [some]. State conditions inside the statement, or at the proof step ("valid once $n \ge 3$"). Extrapolation [skill choice]: with more than two non-trivial conditions, use the `assumption` environment from the kit.
5. **Right after the statement**: intuition and consequence, then the pointer, then implementation.
   - "The identity reduces the problem to a one-dimensional search over $\lambda$, solved by bisection (see Appendix~\ref{apdx:gibbs-lemma} for the proof)." (PriorTilt)
   - "In practice, $\alpha$ is chosen by the calibration error on the validation split (see Algorithm~\ref{alg:cali})." (CaliLoss)
6. **Close the theory** in one of two ways:
   - a `\textbf{Discussion.}` paragraph saying what the result buys [some];
   - the subsection named after the method, which assembles the pieces ("Combining the target term with the divergence budget, PriorTilt ...") [some].
7. **Tie experiments back**: "These results agree with the calibration guarantee of Theorem~\ref{thm:main}." [some]

## 5. Main text vs appendix for math

| Element | Main text | Appendix |
|---|---|---|
| Definitions/lemmas the method needs | full statement, plain numbering "Definition 1", "Lemma 1" [some] | proof |
| Headline theorem (a contribution) | full statement in `restatable{thm}` ("Theorem 3.1") + a 3-5 line proof sketch, introduced by one hand-off sentence: "We sketch the main argument here; Appendix~\ref{apdx:proof-main} gives the full proof." [some] | `\MainTheorem*` restated + full proof |
| Supporting facts (closed form, projection property) | the result + "(see Appendix~\ref{...} for the proof)", or "\Eqref{...} is proved in Appendix~\ref{...}." [some] | one subsection each |
| Derivation of the objective | "The key derivation steps are summarised below (Appendix~\ref{...} gives every step):" + a 2-sentence `derivation_out` [some] | `derivation_full` |
| Extra theory (gradients, metric alignment, complexity) | one takeaway sentence + pointer [some] | "Gradient Analysis" [some]; "Computational Cost Analysis" with algorithm and big-O table [some] |
| Practical caveats | footnote: "In practice, we clip $\hat{p}$ to $[10^{-4}, 1-10^{-4}]$ for numerical stability \citep{Survey-Calibration} (see Appendix~\ref{apdx:numerics})." [some] | analysis subsection |

- All math needed to understand or re-implement the method stays in the main text [all]. Some papers even re-derive the baseline objective in the Preliminary [some]. The appendix never introduces new method equations.
- No full multi-step proof goes in the main text. The longest main-text proof content is a sketch of one sentence + a 2-line `align` [some].

## 6. Appendix proof template (merged from the theory papers, [some])

1. Use one appendix section for all theory, starting with `\clearpage`. Title it "Theoretical Analysis", or mirror the main-text title ("Methodology", `\label{apdx:methodology}`).
2. Give each claim one `\subsection`, in main-text order:
   - "Proof of Theorem~\ref{...}" / "Proof of Lemma~\ref{...}" (with the `\ref`);
   - `\texorpdfstring{Proof of $...$}{Proof of ...}` when the title contains a formula;
   - "Closed-form Solution of ..." / "Complete Derivation of ..." / "Gradient Analysis" for non-theorems.
3. Restate the claim (`\MainTheorem*`).
4. Open with the goal: "We start from the optimisation problem:" / "Our goal is to show that ...". When a tool is needed, recall it first: "We first recall the standard ... inequality".
5. Use bold run-in step heads, each followed by 1-4 sentences and a display. Examples: **Setup.** / **Key identity.** / **Bounding the remainder.**; **Rewriting the objective.** / **First-order condition.** / **Back to the original variables.**
6. Justify every non-trivial step in the glue:
   - "Substituting the closed form of $\boldsymbol{\pi}^{\star}$ into the budget constraint, we obtain:"
   - "The inequality follows from Jensen's inequality, since $-\log$ is convex."
   - "Since the objective is linear and the budget set is convex, the first-order condition is also sufficient. Hence:"
7. Reference main-text equations inside proofs ("by \Eqref{eq:budget-constraint}"). Never renumber the same object.
8. Conclude explicitly: "We conclude:" / "Hence:" + the final display + one sentence on what it guarantees ("The bound therefore holds for every ..."). Name the known problem class it reduces to: "The problem above has the standard entropy-regularised form \cite{...}, so its solution is known in closed form."
9. No lemmas inside proofs. Promote a helper fact to a main-text lemma or to its own subsection.
10. Wrap the steps in `\begin{proof}...\end{proof}`, which gives an end marker.

A compiling skeleton of statement + sketch + appendix restatement is in `templates/chapters/3_methodology.tex` and `templates/chapters/7_appendix.tex`.

## 7. Algorithm blocks

When and where:
- [most] papers have an algorithm: every paper with a multi-step training or inference procedure. Findings papers have none.
- Placement depends on the method type:
  - An iterative training loop goes in the **main text, single column, `[t]`, inside the Methodology next to the optimization subsection** [some] [current].
  - A training-free method keeps its algorithm in the appendix [some], sometimes added only during the rebuttal.
- Reference the algorithm once, in its own sentence: "Algorithm~\ref{alg:method} summarises the full training loop." · "Algorithm~\ref{alg:diva} lists every step of DIVA."

Style ([current]; the papers with a main-text algorithm agree):
- Use `algorithm` + old-style `algorithmic` (`\STATE`, `\WHILE ... \ENDWHILE`, `\IF ... \ENDIF`). Use `algpseudocode` only for a per-line complexity appendix [some].
- Set `\captionsetup{labelformat=default,labelsep=colon,name=Algorithm}`. The caption reads "<Procedure> with <Method>": "Training with DIVA", "Filtering with DIVA".
- Header lines are `\STATE {\bfseries Input:} ...` (a prose list ending with a period) and `{\bfseries Initialization:}`. The last line is `\STATE {\bfseries return} $f_\omega$.`
- Put a gray comment line above each step group (`\CommentBelow{score the current batch}`) and short trailing comments where needed (`\Cmt{update the state}`).
- Use `\leftarrow` for assignment, `\sim` for sampling, `==` and `\texttt{True}` for tests, and `\BREAK` to leave a loop before its last iteration.
- Optional [some]: translucent shaded phases, labelled bottom-right, via `\tikzmk` + `\boxit`.
  - The preamble colours are `PhaseABg` RGB(234,208,228), `PhaseBBg` RGB(250,235,206) and `PhaseCBg` RGB(217,215,219), with labels "Sampling", "Scoring", "Updating".
  - An alternative [some] shades only the novel part.
  - `\boxit` goes OUTSIDE `algorithmic` and needs two compile passes.
  - The skill preamble's `\algoboxshiftx` = -0.6em aligns in one and two columns (ICML skeleton render). [fix] Put `\tikzmk` at the start of a line: a mark at the end of a comment line needs a shift of about -20em instead.
  - In two columns the algorithm takes 0.5 to 0.75 of a column [venue: ICML].
- Compiling example: `templates/chapters/3_methodology.tex`.

## 8. Checklist

- [ ] Calligraphic text objects and sets [all]; hats for generated quantities [most]; bold vectors/matrices [most]; `\mathbb{I}`, `\mathbb{E}`; `\texttt{}` for code-like functions. Skill choices: labels in upright `\text{}` superscripts [skill choice]; `\triangleq` is optional [some].
- [ ] Every display has a colon lead-in, punctuation inside, a "where ..." clause and one so-what sentence. Labels are unique and kebab-case. Use `\Eqref{}` at ICLR [venue: ICLR].
- [ ] A formal result is motivated and self-contained, with a citation in the title if borrowed and the key relation in bold. It has a sketch (headline) or a parenthetical pointer (supporting), an implementation consequence, and a Discussion.
- [ ] Numbering uses one scheme per paper [some]: plain counters ("Definition 1") without a headline theorem; `restatable{thm}` within the section ("Theorem 3.1") with one.
- [ ] Appendix proofs:
  - `\clearpage`, one subsection per claim titled "Proof of Theorem~\ref{}", restated claim;
  - a goal-stating opener ("We start from ..." / "Our goal is ...");
  - bold run-in steps, every step justified;
  - "We conclude:" + display + one guarantee sentence.
- [ ] Algorithm:
  - main text for iterative training [current], appendix for a training-free method [some];
  - caption "<Procedure> with <Method>";
  - bold Input/Initialization/return, gray `\CommentBelow`; shaded phases optional.
- [ ] ICML [venue: ICML]: displays fit 3.25 in via `aligned`, one definition per row, no scaled math (§3 rule 13).
