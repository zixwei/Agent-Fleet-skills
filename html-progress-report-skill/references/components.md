# Component catalog

All components are pure HTML + the CSS/JS already inside `assets/template.html`.
No dependencies; everything works offline (except optional Mermaid). Charts and diagrams render
lazily when their page becomes visible, and re-render on theme switch.

## Which visual for what?

| You want to show… | Use |
|---|---|
| What the agent believes / decided / assumes | **Goal card** + **Claims** (alignment page) |
| Headline numbers and their change | **KPI cards** |
| How far along a plan / sub-tasks are | **Progress bars**, **donut** |
| What's done / in progress / next | **Task board** |
| The core idea in one line | **Idea** card (+ **Analogy**) |
| What changed vs. the usual approach | two **Flow** rows with `.step.x` (removed) / `.step.new` |
| A method's architecture | **Diagram** (JSON → SVG, offline) — preferred over Mermaid |
| Input / sequence layout | **Token strip** |
| How one real input travels through it | **Image flow** (real images/heatmaps) or **Walkthrough** (`ol.walk`) |
| The key equation | **Formula** with annotated terms |
| Why it is built this way | **Why** cards |
| Old vs. new behaviour / design | **Compare** |
| Metric over time / steps / epochs | **Line chart** |
| Comparing variants, ablations | **Bar chart** / **hbar** / **sortable table** |
| Where time/cost goes | **hbar** or **donut** |
| Which files changed | **File tree** |
| The key code change | **Diff** (≤20 lines) |
| A decision / risk / win / side note | **Callouts** |
| Long details (commands, logs, configs) | `<details>` |

---

## Status badges
```html
<span class="badge ok"><span class="dot"></span>On track</span>
<span class="badge warn">At risk</span>
<span class="badge bad">Blocked</span>
<span class="badge info">Exploring</span>
<span class="badge neutral">paused</span>
```

## KPI cards
Add `ok` / `warn` / `bad` to `.kpi` to colour its top bar.
```html
<div class="kpis">
  <div class="kpi"><div class="label">Tests passing</div><div class="value">48/48</div><div class="delta up">▲ +6 since last</div></div>
  <div class="kpi"><div class="label">Val AUROC</div><div class="value">0.947</div><div class="delta up">▲ +3.5 pt</div></div>
  <div class="kpi"><div class="label">Epoch time</div><div class="value">41 s</div><div class="delta up">▼ 2.3× faster</div></div>
  <div class="kpi"><div class="label">Open issues</div><div class="value">2</div><div class="delta flat">— unchanged</div></div>
</div>
```
`delta up` = good (green), `down` = bad (red), regardless of arrow direction.

## Progress bars
```html
<div class="progress-row"><span>Data pipeline</span><div class="progress"><span class="ok" style="width:100%"></span></div><span class="small muted">100%</span></div>
<div class="progress-row"><span>Model</span><div class="progress"><span style="width:60%"></span></div><span class="small muted">60%</span></div>
<div class="progress-row"><span>Eval</span><div class="progress"><span class="warn" style="width:20%"></span></div><span class="small muted">20%</span></div>
```

## Task board
```html
<div class="board">
  <div class="col"><h4>✅ Done</h4><div class="card">Loader rewrite<span class="small muted">a1b2c3d</span></div></div>
  <div class="col"><h4>🔄 Doing</h4><div class="card">Ablation on prompt length<span class="small muted">3/5 runs</span></div></div>
  <div class="col"><h4>⏭ Next</h4><div class="card">Write README</div></div>
</div>
```

## Callouts
```html
<div class="callout decision"><strong>🤔 Needs your decision</strong>
  Use backbone A or B?
  <ol><li><b>A</b> — faster, −1 pt accuracy</li><li><b>B</b> — +1 pt, 2× memory</li></ol>
  Recommendation: <b>A</b> (fits GPU budget). Default if no reply: A.</div>
<div class="callout risk"><strong>⚠ Risk</strong> Eval set may overlap with train (checking).</div>
<div class="callout win"><strong>🎉 Win</strong> CI is green for the first time this week.</div>
<div class="callout note"><strong>ℹ Note</strong> Numbers below use seed 0 only.</div>
```

## Flow diagram (offline, CSS only)
```html
<div class="flow">
  <div class="step"><b>1. Load</b><span class="small muted">images → tensors</span></div>
  <div class="step hl new"><b>2. Cache features</b><span class="small muted">computed once, reused</span></div>
  <div class="step"><b>3. Score</b><span class="small muted">distance to prompts</span></div>
  <div class="step"><b>4. Report</b><span class="small muted">AUROC / PRO</span></div>
</div>
```
`hl` highlights a step, `new` adds a NEW tag (use for what changed).

## Pages
Every `<section class="page">` becomes a sidebar entry; `data-nav` = label, `data-icon` = emoji.
Extra pages go right after `<!-- @pages:insert -->`. Links like `<a href="#results">` switch pages;
links to an element id inside another page switch and scroll.
```html
<section class="page" id="data" data-nav="Data" data-icon="🗂">
<!-- @data:start -->
  <header class="page-head"><div class="kicker">Data</div><h1>Outcome-first title</h1><p class="lede">One line.</p></header>
  …
<!-- @data:end -->
</section>
```

## Alignment: goal card + claims
The page `#align` holds one `.goal` card and several `.claims` lists. The template's JS adds the
✓ / ✗ / ? / ✎ buttons, the evidence badge, local persistence, the sticky counter bar with **Copy feedback**,
the sidebar count and the banner on the overview page — you only write the HTML below.
```html
<div class="goal">
  <div class="lead"><b>Goal (my words)</b><span>One sentence.</span></div>
  <div><b>Done when</b><ul><li>…</li></ul></div>
  <div><b>Constraints</b><ul><li>…</li></ul></div>
  <div class="no"><b>Not doing</b><ul><li>…</li></ul></div>
</div>
<h2>🧭 Decisions I made on my own</h2>
<div class="claims">
  <div class="claim" data-id="d1" data-kind="decision" data-evidence="code" data-since="a1b2c3d">
    <div class="c-body"><b>Checkable one-line statement.</b><span>Why · rejected alternative · cost to reverse.</span></div>
  </div>
  <div class="claim" data-id="a1" data-kind="assumption" data-evidence="guess" data-since="a1b2c3d">
    <div class="c-body"><b>…</b><span>…</span></div>
  </div>
  <!-- after the user confirmed it in the chat: data-user="confirmed" -->
</div>
```
`data-kind`: `decision | assumption | deviation | question`. `data-evidence`: `run | code | doc | guess`.
IDs must be unique and stable (feedback is keyed on them). An optional `<div class="c-meta">` inside
`.c-body` adds extra small facts (file paths, numbers).

## Idea card (method page opener)
```html
<div class="idea"><span class="big">🎯</span><b>Discrimination needs two vectors, not language.</b><span>One muted sentence of how.</span></div>
```

## Flow with removed / new steps (old vs. new)
```html
<div class="imgflow-row-label">Before</div>
<div class="flow"><div class="step"><b>Image encoder</b></div><div class="step x"><b>Text encoder</b><span class="small muted">prompts</span></div><div class="step"><b>Similarity</b></div></div>
<div class="imgflow-row-label">Ours</div>
<div class="flow"><div class="step"><b>Frozen ViT</b></div><div class="step hl new"><b>+2 learnable tokens</b></div><div class="step"><b>Cosine diff</b></div></div>
```

## Diagram (architecture, JSON → SVG, offline)
Grid layout: each node sits at `col`/`row` (fractions allowed, e.g. `"row":1.5` to centre between rows).
Node fields: `id`, `label`, `sub` (shape/role, mono), `kind` = `data | frozen | train | out | new | op`,
`img` (relative path → image node), `span` (columns), `w`/`h`, `icon`, `note` (tooltip).
Edges: `["from","to","label"]` or `{"from","to","label","dashed":true,"kind":"train"}` (kind colours the edge).
Same column → vertical edge; right → curved; left → loops underneath.
Groups: `{"label","nodes":[ids],"kind"}` draws a dashed frame (use for "frozen backbone", "repeated per layer").
Options: `data-col-width` (default 190), `data-row-height` (92), `data-caption`, `data-legend` (rename or `null` to hide a kind).
Hovering a node highlights its edges. Keep labels short (≤10 CJK chars / 16 latin); put detail in `sub`.
```html
<div class="diagram" data-col-width="180" data-caption="Hover a node to trace it."
  data-legend='{"frozen":"frozen ❄","train":"trainable 🔥","data":"tensor","out":"output"}'
  data-nodes='[
    {"id":"img","img":"assets/sample.jpg","label":"input","sub":"518×518","col":0,"row":0.5},
    {"id":"enc","label":"Encoder","sub":"[1369×1024]","col":1,"row":0,"kind":"frozen"},
    {"id":"head","label":"Head","sub":"MLP","col":1,"row":1,"kind":"train"},
    {"id":"cos","label":"cos","col":2,"row":0.5,"kind":"op"},
    {"id":"out","label":"score 0.83","col":3,"row":0.5,"kind":"out"}]'
  data-edges='[["img","enc","patches"],["enc","head"],["head","cos"],{"from":"cos","to":"out","label":"top-1%"}]'
  data-groups='[{"label":"backbone","nodes":["enc"],"kind":"frozen"}]'></div>
```

## Token strip (sequence / input layout)
```html
<div class="tokens">
  <i class="t a train">t<sub>a</sub></i><i class="t n train">t<sub>n</sub></i><i class="t c">CLS</i>
  <i class="t p"></i><i class="t p"></i><i class="t p"></i><span class="more">… ×1369</span>
  <span class="tcap">Caption: what the sequence is and which parts train.</span>
</div>
```
`.a` red, `.n` green, `.c` blue, `.p` grey patch; `.train` adds an orange halo. Custom colour: `style="--c:#…"`.

## Image flow (real example, end to end)
Real images from the project (≤160 px thumbnails in `./assets/`). Arrows are automatic; the row never wraps
(scrolls on small screens). `figure.hl` / `.ok` / `.bad` outline a frame; `.verdict.ok|bad` ends the row.
Click any image to zoom.
```html
<div class="imgflow-row-label">bottle / broken_large</div>
<div class="imgflow">
  <figure><img src="assets/x.jpg" alt=""><figcaption><b>input</b><span>broken rim</span></figcaption></figure>
  <figure><img src="assets/x_l6.png" alt=""><figcaption><b>layer 6</b><span>max 0.85</span></figcaption></figure>
  <figure class="hl"><img src="assets/x_fused.png" alt=""><figcaption><b>fused</b></figcaption></figure>
  <div class="verdict bad"><span class="v">NG</span><span>3.09 &gt; 0.043</span></div>
</div>
```
Single image with caption: `<figure class="figure"><img src="…"><figcaption class="cap">…</figcaption></figure>`.

## Formula with annotated terms
```html
<div class="formula">
  <span class="term" data-note="is it like 'anomaly'?" style="--c:var(--bad)"><span>cos(p, t̃<sub>a</sub>)</span></span>
  <span class="op">−</span>
  <span class="term" data-note="is it like 'normal'?" style="--c:var(--ok)"><span>cos(p, t̃<sub>n</sub>)</span></span>
</div>
```

## Mermaid (richer diagrams; needs network, shows source text offline)
```html
<pre class="mermaid">
flowchart LR
  A[Input] --> B{Cached?}
  B -- yes --> C[Load cache]
  B -- no --> D[Compute] --> C
</pre>
```

## Analogy (method section)
```html
<div class="analogy"><div><b>Like a restaurant kitchen's mise en place</b>
Chefs chop all vegetables once before service, then just grab them per order. We compute image
features once, then every epoch just grabs them. <span class="muted">(Where it breaks: our "vegetables" never spoil, so the cache never expires.)</span></div></div>
```

## Walkthrough: one concrete example, step by step
```html
<ol class="walk">
  <li><b>Input:</b> <code>screw_017.png</code>, 1024×1024, a thin scratch near the head.</li>
  <li><b>Features:</b> backbone → <code>[1369, 768]</code> patch vectors, saved to cache (first epoch only).</li>
  <li><b>Compare:</b> each patch vs. 8 "damaged" prompts → similarity map; scratch patches score <code>0.83</code>, others <code>≈0.21</code>.</li>
  <li><b>Result:</b> image score <code>0.83</code> &gt; threshold <code>0.5</code> → flagged as defective ✅</li>
</ol>
```

## Why this design (choices and rejected alternatives)
```html
<div class="why">
  <div><span class="ic">🗄</span><b>Memory-mapped file</b>Survives restarts, and the OS handles paging.<span class="rejected">✗ In-RAM dict: 14 GB, lost on crash</span></div>
  <div><b>Key = file hash</b>Edited images invalidate themselves automatically.<span class="rejected">✗ Key = path: stale after edits</span></div>
</div>
```

## Compare (before / after)
```html
<div class="compare">
  <div><h4>Before</h4><p>Features recomputed every epoch (≈95 s).</p></div>
  <div><h4>After</h4><p>Computed once and memory-mapped (≈41 s).</p></div>
</div>
```

## Charts (built-in SVG renderer)
Attributes: `data-type` = `line | bar | hbar | donut`; `data-labels` = JSON array;
`data-series` = JSON array of `{"name","values",["color"],["colors"],["dashed"]}` (`colors` = per-bar colours, e.g. highlight the winner);
optional `data-title`, `data-unit`, `data-min`, `data-max`, `data-height`, `data-target` (line only),
`data-center` / `data-center-label` (donut). Use single quotes around JSON attributes.

```html
<div class="chart" data-type="line" data-title="Val AUROC per epoch" data-target="0.95"
  data-labels='["1","2","3","4","5"]'
  data-series='[{"name":"baseline","values":[0.88,0.90,0.91,0.91,0.912],"dashed":true},{"name":"ours","values":[0.89,0.92,0.94,0.945,0.947]}]'></div>

<div class="chart" data-type="bar" data-title="Ablation"
  data-labels='["full","−cache","−prompt ens."]'
  data-series='[{"name":"AUROC","values":[94.7,94.6,92.1]}]' data-unit="%"></div>

<div class="chart" data-type="hbar" data-title="Where epoch time goes (s)"
  data-labels='["data loading","forward","backward","logging"]'
  data-series='[{"values":[12,15,11,3]}]' data-unit=" s"></div>

<div class="chart" data-type="donut" data-center="7/10" data-center-label="tasks done"
  data-labels='["done","doing","todo"]' data-series='[{"values":[7,2,1]}]'></div>
```

## Sortable table
Wrap in `.tablewrap` for rounded borders + horizontal scroll; `td.pos` / `td.neg` colour deltas.
```html
<div class="tablewrap"><table class="sortable">
  <thead><tr><th>Variant</th><th class="num">AUROC</th><th class="num">Time (s)</th></tr></thead>
  <tbody>
    <tr class="best"><td>ours</td><td class="num">94.7</td><td class="num">41</td></tr>
    <tr><td>baseline</td><td class="num">91.2</td><td class="num">95</td></tr>
  </tbody>
</table></div>
```

## File tree
```html
<pre class="tree">
src/
├── <span class="mod">loader.py</span>        ~ memory-mapped reads
├── <span class="add">cache.py</span>         + new
└── <span class="del">old_loader.py</span>    − removed
</pre>
```

## Diff (keep ≤20 lines; escape &lt; &gt; &amp;)
```html
<pre class="diff"><code>@@ loader.py @@
-feats = model(x)
+feats = cache.get(key) or cache.put(key, model(x))
</code></pre>
```

## Details (collapse everything non-essential)
```html
<details><summary>Commands & files</summary>
<pre><code>python train.py --cfg cfg/a.yaml</code></pre>
</details>
```
