# Paper types: intro P2-P4 and Methodology skeletons per type

The chapter scaffolds in `templates/chapters/` are type-neutral. This file gives the wording and section plan for each method type as a worked pattern, so a paper that is not a workflow method still has a scaffold. P1 and P5 of the intro, the abstract slots, the Experiments skeleton and the Conclusion are shared by all types (`prose.md` §2-3, `experiments.md` §2). All examples are synthetic: four invented methods (DIVA, PriorTilt, CaliLoss, SynthBonus) show the techniques; "..." marks where your content goes. Evidence labels as in SKILL.md.

| Type | Use when the contribution is ... | Algorithm | Formal results | Evidence |
|---|---|---|---|---|
| W. Workflow with components | new modules or scorers inside a training, agent or inference loop, trained end to end | main text | none | `[some]` |
| T. Objective + constraint, closed form | a learnable operator defined by two objectives, solved in closed form; inference-time or training-free | appendix (with a complexity table) | Definition + Lemma (borrowed, cited) | `[some]` |
| L. Loss with theory | a new training loss derived from an idealised criterion, with a guarantee | appendix | restatable Theorem + sketch | `[some]` |
| R. Reward design for RL | a new reward signal or supervision source, often a dense signal built from a cheap external estimate | main text | none | `[some]` |
| F. Findings | an empirical discovery, possibly followed by a simple method | none | none (probes, regressions) | `[some]` |

The type scaffolds W, T, L, R are patterns (`[skill choice]`): the invented examples show one way to fill each, and only the cross-type rules in SKILL.md are binding. Type F is covered in `prose.md` §2-3 (findings variants) and `structure.md` §1.

---

## Type W: workflow with components (example: DIVA, data filtering with two scorers)

**Intro P2, prior paradigm made concrete.** "To cut this cost, recent work <Prior> \cite{Placeholder-A} filters the training pool with a proxy model before fine-tuning .... As illustrated in Figure~\ref{fig:teaser-figure}, rather than training on the full corpus \cite{}, this approach scores every candidate once and keeps the top-ranked fraction .... Concretely, a frozen proxy $g$ assigns each example $x_i$ in the pool $\mathcal{D}$ a score $g(x_i)$, and the $k$ highest-scoring examples form the training set .... In practice, .... This filtering paradigm has shown promise in reducing ...."

**Intro P3, the pair of limitations as bullets.** "However, a single fixed score carries over known weaknesses of greedy selection \cite{}. We identify two limitations that can hurt both the coverage and the precision of the kept data:" + two `\item \textbf{Loss of Rare Clusters.}` / `\item \textbf{Crowding by Near-Duplicates.}` bullets of 85-115 words (mechanism → consequence → amplifier → gap).

**Intro P4.** "To this end, we introduce X, a ... filtering framework for cheap and faithful ..., motivated by the success of two-signal selection rules in active learning \cite{} (\eg ...). The key idea is to equip the training loop with two learned scorers --- a difficulty scorer and a diversity scorer --- which set (i) how much weight each example receives and (ii) how many examples each cluster contributes. Specifically, at step $t$, ... which can be written as $p_t(x_i) \propto w_i\, q_{c(i)}$. ... To give the scorers these abilities, we train them with two feedback signals ... jointly with the model .... The difficulty scorer raises ..., while the diversity scorer protects ...; together, they make filtering cheaper and more faithful ...."

**Methodology plan:**
```
3   Methodology   "This section presents X, a ... filtering loop with two scorers, for cheap and faithful ...."
                  roadmap: "We first describe how X samples each batch in Section .... After that, we explain how the scorers are trained ..."
3.1 <Two-Scorer Sampling>            purpose clause ("To keep rare clusters and to avoid near-duplicates, we add two scorers ...,
                                     following two-signal selection rules from active learning \cite{}"); ONE display for the step
                                     ($p_t(x_i) \propto w_i\, q_{c(i)}$ with $w_i = g^{\text{diff}}_\omega(x_i)$, $\mathbf{q}_t = g^{\text{div}}_\omega(\cdot)$);
                                     each factor in prose with an inline "(\ie $q_c \propto |\mathcal{C}_c|^{-\gamma}$ at initialisation)";
                                     prompt/text box as a figure (when a module reads instructions); figure* of the pipeline;
                                     "Algorithm~\ref{} summarises the full loop of X."
3.2 Scorer Training with Held-Out Feedback
    3.2.1 Signal Design              one run-in per signal, each a `cases` display with \text{} superscripts ($\ell^{\text{div}}_t$)
    3.2.2 Score Normalisation        figure + per-batch and per-cluster normalised scores
                                     + "Finally, the total score ... combines these two"
3.3 Inference                        how the components act at test time (here: when filtering a new corpus)
```
Algorithm: main text, single column, `[t]`, shading only the novel part.

---

## Type T: objective + constraint, closed form (example: PriorTilt, training-free label-shift correction)

**Intro P1-P2, paradigm with a concrete quoted example and its formula.** "When a classifier moves to a new site, the class balance often changes with it \citep{} (\eg a screening model trained where 5\% of scans are positive is deployed where 30\% are). Prior correction by Bayes' rule \citep{} has become the standard fix, and it needs no retraining \citep{}. As shown in Figure~\ref{}, each predicted posterior $p(y \mid x)$ is reweighted as $p'(y \mid x) \propto p(y \mid x)\,\pi'_y/\pi_y$, where $\boldsymbol{\pi}$ and $\boldsymbol{\pi}'$ are the training and deployment priors .... The deployment prior is usually estimated from unlabelled predictions ...."

**Intro P2-P3, trade-off + two prevailing strategies + shared flaw + evidence.** "However, while simple, trusting one estimate of $\boldsymbol{\pi}'$ creates a trade-off between fitting the new site and staying close to the training prior --- a noisy estimate from a few hundred unlabelled inputs can swing the prior far from both (\eg assigning a rare class half of the mass). Two common remedies exist: EM-style re-estimation \citep{} and confusion-matrix inversion \citep{}. [one sentence each] Yet both return a point estimate without any control on how far it moves, which limits .... Taking <Prior> \citep{} as an example, we compare ... in Figure~\ref{}. Intuitively, an effective correction should ...: it should explain the unlabelled predictions of the new site (termed \emph{target fit}), while moving the prior only as far as the evidence allows (termed \emph{prior fidelity}). This gap calls for a formulation with an explicit budget."

**Intro P4, method as a formula with one role per objective.** "To this end, we build on the Gibbs variational principle \citep{} and propose PriorTilt, a training-free correction that finds the deployment prior with the best target fit inside a divergence budget around the training prior, thereby achieving both target fit and prior fidelity. The central idea is to tilt the training prior toward the mean prediction $\bar{\mathbf{p}}$ of the new site, which gives the closed form $\boldsymbol{\pi}^{\star} \propto \boldsymbol{\pi}^{\text{src}} \odot \exp(\bar{\mathbf{p}}/\lambda)$, where $\lambda$ is set by the budget $\epsilon$. For a small shift, $\lambda$ is large and $\boldsymbol{\pi}^{\star}$ stays near $\boldsymbol{\pi}^{\text{src}}$; for a large shift, .... PriorTilt is training-free and bounded, .... As shown in Figure~\ref{}, ...." There is no training sentence: the method is closed form, and the P5 closer says so ("without retraining the classifier").

**Preliminary:** roadmap → 2.1 the base operation as a display ($p'(y \mid x) \propto p(y \mid x)\,\pi'_y/\pi_y$) + how its ingredient is computed (the prior estimate from unlabelled predictions) → 2.2 `Literature Review`: "Existing estimators differ in what they match, the likelihood of the new predictions or the confusion statistics of the old ones, and we group them as <family A> and <family B>:" + two bold-headed bullets + critique.

**Methodology plan:**
```
3   Methodology   "This section presents X, a training-free and bounded ... method for A and B."
                  roadmap first / After that / Then / Finally
3.1 A Budgeted <Estimate> with Two Objectives
      general formula (display) -> "Specifically, ... is chosen to meet two objectives:"
      itemize: \textbf{Target fit.} one sentence / \textbf{Prior fidelity.} one sentence
      "The next two subsections show how each objective is met.
       For readability, we drop the site index from here on."
3.2 <Objective A> via <Technique>        (e.g. "Target Fit via Expected Log-Likelihood")
      purpose clause -> objective display (\max_{\boldsymbol{\pi}} \langle \boldsymbol{\pi}, \bar{\mathbf{p}} \rangle) -> "where ..." with dimensions
      -> \begin{definition}[Kullback-Leibler Divergence \citep{Info-Theory}] -> budget set \{\boldsymbol{\pi} : \mathrm{KL}(\boldsymbol{\pi}\,\|\,\boldsymbol{\pi}^{\text{src}}) \le \epsilon\}
3.3 <Objective B> with <Technique>        (e.g. "Prior Fidelity with a Divergence Budget")
      "With the target term in place, we now bound ..." -> Lagrangian display
      -> "However, solving ... by projected gradient needs many passes over .... We therefore rely on the following lemma:"
      -> \begin{lemma}[Gibbs Variational Principle \citep{<source>}] -> "(proof in Appendix~\ref{})"
      -> "With Lemma~\ref{}, the problem reduces to ..." -> closed-form display -> one-dimensional search for $\lambda$ -> so-what
3.4 <Method name>                          final function display + "Combining ... with ..., X ..." + implementation pointer
```
Numbering: "Definition 1", "Lemma 1" (plain counters, `preamble.tex` B6). No algorithm in the main text; the appendix has the procedure and a complexity table.

---

## Type L: loss with theory (example: CaliLoss, a temperature-free calibration loss)

**Intro P3-P4.** Limitations as two bold bullets ("Binning makes the calibration term non-differentiable." / "No stated link between the training loss and the reported calibration error."), then "To this end, we replace the binned calibration error with a kernel-smoothed surrogate and derive a temperature-free variant of the negative log-likelihood from it. Specifically, we smooth each confidence with a kernel of bandwidth $h$ and ...." P4 then names two useful properties with "On the one hand, ... On the other hand, ...", each ending with a pointer "(\cf Appendix~\ref{})" / "(\cf Theorem~\ref{thm:main})".

**Methodology plan:**
```
3   Methodology (or "Methodology of X")   roadmap: "we first derive X ... Then we establish its properties ..."
3.1 <Target Criterion>           the quantity the paper cares about, written exactly (here: the binned ECE)
                                 key identity with \underbrace{}_{\text{...}} naming both sides
3.2 Deriving X                   "The binned form, however, has zero gradient almost everywhere.
                                 The question is therefore how to ..." -> loss display
                                 -> "We sketch the main steps below (Appendix~\ref{} gives the full derivation):"
                                 -> derivation environment
3.3 Theoretical Analysis of X    motivation ("..., how X relates to the reported metric has not been stated.
                                 We make the relation precise below")
                                 -> \begin{restatable}{thm}{MainTheorem} with the key relation in bold ("\textbf{upper-bounds}")
                                 -> "We give a proof sketch here and defer the full proof to Appendix~\ref{}."
                                 -> \textbf{Discussion.} what the result buys; implementation knob "(see Algorithm~\ref{})"
```
Numbering: "Theorem 3.1" (`thm`, numbered within the section). Define with `\triangleq`.

---

## Type R: reward design for RL (example: SynthBonus, RL for molecule generation)

**Intro P2, challenge + one bold-italic question.** "However, while useful, rewarding synthesizability during generation is hard for two reasons: (i) exact route planning is too slow to run on every rollout \cite{}, and (ii) cheap estimates are noisy, and a policy trained on them learns to exploit their errors \cite{}. \textit{\textbf{Can a cheap route-length estimate guide RL toward makeable molecules without the policy learning to exploit it?}} If so, synthesizability could be optimized while molecules are generated rather than filtered afterwards, at little extra compute."

**Intro P3, an enabling research line.** "We see a route to this in two established tools. On the one hand, potential-based reward shaping \cite{} adds a dense signal to sparse rewards without changing which policy is optimal .... On the other hand, uncertainty penalties from model-based RL \cite{} discount rewards where a learned model is unreliable .... Combined, the two tools suggest that a noisy estimate can serve as a safe dense reward. However, how to ... for partial molecules is still open."

**Intro P4, signal + safeguard + formula.** "To close this gap, we introduce \textbf{SynthBonus}, a shaped synthesizability bonus that extends RL for molecule generation with .... The central idea is to turn a cheap route-length estimate into a potential over partial molecules and to discount it where an ensemble of estimators disagrees. Figure~\ref{} gives an overview of the process. Specifically, each step receives $\tilde{r}_t = r_t + \gamma\Phi(\hat{\mathcal{G}}_{t+1}) - \Phi(\hat{\mathcal{G}}_t) - \beta\,\sigma(\hat{\mathcal{G}}_{t+1})$, where $\Phi$ is the mean estimate of the ensemble and $\sigma$ its spread (\ie ...). Overall, SynthBonus lets RL go beyond outcome-only optimization by ...."

**Preliminaries:** 2.1 "RL for Molecule Generation" (rollout display, property reward display, "However, under outcome-only rewards, a partial molecule $\hat{\mathcal{G}}_t$ receives no signal until the episode ends ...") → 2.2 "Potential-Based Reward Shaping" (the shaping identity, with its citation).

**Methodology plan:**
```
3   Methodology   the method name again (with its acronym expansion, if any); "In the rest of this section, we first outline the key idea
                  ... to give a high-level picture. Then, ... After that, ... Finally, ..."
3.1 Key Idea                          key-idea figure (wrapfigure in one column); signals named with \text{} superscripts
3.2 A Potential from <Estimate>       "This subsection looks more closely at ..." + \textbf{Estimator ensemble.} ... / \textbf{Potential.} ... run-ins
3.3 Penalising <Unreliable Signal>    argue then propose: "While shaping speeds up learning, we argue that the policy may drift
                                      toward molecules where the estimate is wrong .... We therefore ..." + penalty display
3.4 Shaped Reward and Optimization    \textbf{Total reward.} display + "This reward lets ..."; "Algorithm~\ref{} gives the full
                                      optimization procedure." Algorithm with the novel steps shaded
```
Write reward labels as `\text{}` superscripts ($r^{\text{prop}}$) `[skill choice]`, not italic subscripts ($r_{prop}$).

---

## What changes in the Experiments section by type

| | W | T | L | R |
|---|---|---|---|---|
| Setup heads | `\textbf{Models.}` `\textbf{Evaluation Benchmarks.}` | `\textbf{Models.}` + one head per benchmark family (e.g. `\textbf{Synthetic shifts.}`, `\textbf{Natural shifts.}`) + `\textbf{Baselines.}` | `\textbf{Baselines.}` `\textbf{Datasets.}` | `\textbf{Models.}` `\textbf{Training and evaluation.}` under `\subsection{Experiment Setup}` |
| Baselines | one natural prior method, named in RQ1 | ≥3 methods from ≥2 families + an ablation baseline + one related method | ≥2 families (e.g. post-hoc and train-time calibration) | a single baseline (the plain training recipe) named in the performance RQ, with the shared starting checkpoint |
| Main table | T4 (quality + cost rows) | T2 (per backbone, Avg, "(Ours)") | dataset-grouped | T1 (scale-grouped, no Avg) |
| Mechanism RQ | per-component run-ins, constructed stress setting | budget sweep + a plot of estimated vs true priors | a study of the key knob and of training length | a reward-hacking check (true vs shaped reward over training), a shuffled-signal control, then performance |
