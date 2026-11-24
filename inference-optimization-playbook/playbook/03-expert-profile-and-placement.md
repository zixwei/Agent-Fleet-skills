# Hot experts: profile, domain, placement

Covers DeepSeek V4.1 Flash, GLM-5.3-Flash, MiniMax-M3.1-Flash, Hy4 Flash.

> **Read when:** you choose which experts stay in VRAM, or a profile gives fewer hits than expected. **Skip when:** routing has weak skew (go to 04), or everything fits in VRAM. **Ledger tags:** `#offload` `#strong-skew`.

The question here: how do you find which experts are hot on a specific load (for example a business domain), and how do you place them.

## What a profile is

A profile is pick counters: how many times the router picked each expert of each layer on a representative load. Shape `[layers × experts]`. For DeepSeek it is the file `expert-profile.bin`, 43×384 uint32 counters (40 trunk layers plus 3 drafter blocks). In Maya/Strata it is passed with `--expert-profile`.

How you get one in practice:

- **Accumulate from normal work.** The engine appends counters on every run. Our DeepSeek service profile grew this way: 75.8 million picks per layer from agent work. 2 days of new sessions added only 0.5%, so the profile stabilizes fast.
- **Router trace.** A separate mode that writes the picks of every token (`DS4_ROUTE_TRACE` for DeepSeek, our own `route_trace.py` for GLM). You can build a profile from a trace any way you want and look at prefill and decode separately.
- **Atlas by domain.** For GLM we recorded picks on 17,078 records from 14 domains, 12.6 million positions per layer (in the cloud on 4x RTX PRO 6000). This lets you compare domains with each other.

## How much the profile depends on the domain

Direct data. A check on the picks of the DeepSeek service itself over 2 days (68 thousand tokens of agent work): what share of picks do the top-180 experts of a layer (of 384) cover, if the ordering comes from different profiles.

| Ordering taken from | Coverage |
|---|---|
| ideal: the same segment | 94% |
| profile accumulated on similar work over previous weeks | 86% |
| synthetic load with the real order of agent turns | 86% |
| stock profile shipped with the engine | 68% |
| stock + 1 benchmark run | 68% |

What this means for a domain load:

- **A profile from similar work is almost as good as the ideal** (86% vs 94%). If requests in the domain are similar, it is enough to accumulate a profile on real domain traffic.
- **A foreign profile costs real speed, at least at the start.** The first run of the UD quant of DeepSeek started with almost the stock ordering: by the static placement model 51% of picks went past VRAM vs 30% with the service profile. The calculation gave ~42 tok/s vs 36 when the profile changed (calc). A live cache catches up part of the gap over time. How much exactly, we did not test.
- **Synthetic load is not always bad,** if it has the real structure of requests (agent turns with tools). But we rolled back a night of synthetic benchmarks from the service profile so it would not shift the placement.

GLM domain data (atlas):

- Spearman correlation of a domain's expert ranking with the overall one: agent 0.895, code 0.850, reasoning 0.818, knowledge 0.745, general 0.713. Agent and code traffic are close to the overall profile, knowledge and general chat differ.
- **Zero coverage.** A profile taken on one domain leaves some experts with no picks at all. In the vision OCR subset (10 records) 29 experts per layer got no tokens, vision charts 20, code and agent had none. On the full corpus there are 0 dead experts. An expert that is "dead" on your calibration can be needed by another input type. Rule: **if the work includes images, tables or another language, they have to be in the profile**, especially if you cut anything by profile ([10](10-pruning-and-merging.md)).
- Note: the share of records in a domain correlates with its correlation (0.66), so the table partly shows sample size.

On models with weak global skew a profile gives little for any domain. MiniMax: a profile from 1 answer gave 90% hits on that answer and 66-69% on other answers, a profile from 2 other answers 64-68%, random placement 61%. Every answer has its own working set, and a static set does not catch it.

Hy4 Flash shows the same thing from another side. 220 experts chosen by the stats of the *input* covered only 48% of picks during generation on one long text. Chosen by the stats of the *generation itself*: 96% on the same material. The second number is the locality headroom. In a running service you do not know the profile of the next answer in advance.

## How to place experts by profile

The rule Maya/Strata uses for DeepSeek (checked with our simulator):

1. Layers are cut into contiguous pieces per card (layer split). Every card has its own VRAM budget for experts.
2. For every card, greedily take the expert with the highest **share of picks per byte**. Plain share of picks is not enough, because experts of different layers in a mixed quant have different sizes.
3. The share is smoothed toward uniform: `0.9 × profile + 0.1 × uniform`. Rare but not dead experts do not drop out completely.
4. One slot per layer stays free for live cache swaps.
5. The rest goes to RAM.

The simulator of this rule on the service profile matched the engine log within 1%. **Improving the rule itself was not needed:** without the division by bytes, with one shared budget for all cards, with the drafter on another card, everything stayed within 1-2%.

Pseudocode for your own system:

```python
# profile[layer][expert] = pick count on a representative load
# size[layer][expert]    = bytes per expert in the chosen quant
# budget[gpu]            = VRAM bytes for experts on the card; layers_of[gpu] = layers of this card
for gpu in gpus:
    cand = []
    for L in layers_of[gpu]:
        total = sum(profile[L])
        for e in range(n_experts):
            share = 0.9 * profile[L][e] / total + 0.1 / n_experts
            cand.append((share / size[L][e], L, e))
    cand.sort(reverse=True)
    used = reserve_one_slot_per_layer(gpu)
    for _, L, e in cand:
        if used + size[L][e] > budget[gpu]:
            continue
        place_in_vram(gpu, L, e); used += size[L][e]
```

## Reserve the fixed consumers first (Hy4 Flash)

On Hy4 the same binary with an automatic budget could place a different number of experts. This also affected the comparison of NVMe modes. The fix: first reserve KV, the vision projector and work buffers, then distribute experts.

For every run write down the real placement per layer, the scratch sizes and the free VRAM after start. Without this an A/B compares 2 different placements.

## How to validate a profile before launch

Do not trust the "concentration" of a profile on itself. In the short profile shipped with our DeepSeek quant, the top-64 cover 80.5% of its own picks. In the service profile only 57.6%. It looks like the short one is better. But short profiles (stock, the UD pack profile) cover 68% on a real session vs 86% for the service profile. A narrow profile looks concentrated because it was taken on a small and uniform set.

The right check:

1. Split domain traffic into 2 parts by time (for example weeks 1-2 and week 3).
2. Build the profile on the first part.
3. On the second part compute pick coverage at your VRAM budget (how many experts per layer fit).
4. Compare with the ideal (profile from the second part itself) and with the stock profile.

If the gap "your profile vs ideal" is large (ours was 8 points, the 10 point threshold is a heuristic), update the profile more often, or the domain is not uniform and the live cache matters more ([04](04-live-expert-cache.md)).

## Profile traps

- **The profile only grows.** The engine appends to it on every run. For an A/B every arm needs its own copy of a frozen profile, or the arms change each other's placement.
- **The profile can fail to save.** In MiniMax `save_profile` ran only on a clean exit, and the service was stopped with SIGTERM. The "service profile" turned out to be a snapshot of ~300 tokens of 1 session.
- **The profile is almost all prefill.** Prefill gives thousands of picks per turn, decode hundreds. If your load has long answers and short or cached prompts, the decode distribution can differ. Keep prefill and decode counters separate and weight them by the real share of time.
- **Drafter counters can be zero.** In DeepSeek the drafter block counters were 0 in all 10 profiles we checked, so the rule "keep experts that give 99% of routes" never fired for it. The drafter went to GPU0 entirely and its VRAM cap cut blindly by index.
- **One profile for 2 quants.** We used the Maya-S profile for the UD quant of the same model. The routers are dense and should match. This was confirmed indirectly on 1 text, not checked directly.
- **Someone else's stats.** Our GLM runs in Maya had profile accumulation turned off, and warmup used the upstream profile. We found this only during the review.

## Allocate global KV as the request grows (Inkling 2 Mini, 2026-11-21)

On one RTX 3090, the 3,072 experts of the UD-Q4_K_XL checkpoint occupy 14.7 GiB. The eight logical global-attention visits need 16 GiB of FP8 K/V at 1,048,576 positions. Eagerly allocating the maximum before the expert cache left 285 expert slots. A 47-token prompt then decoded at 8.72 tok/s (31 timed tokens, one run). Allocating the global KV with CUDA managed memory allowed the engine to keep all experts on the card during short requests. Sliding-window KV remained in dedicated GPU memory.

The managed allocation changes residency as the prompt grows. Benchmark the short request with the maximum still configured, then measure decode on an actual full prompt. A short request measures the virtual reservation and expert residency. Only the deep request incurs the cost of scanning a populated cache.

## Every expert in VRAM on one card: the resident token path (Inkling 2 Mini, 2026-11-21)

When the experts fit (Inkling 2 Mini: 14.7 GiB of experts, 1.1 GiB of dense weights kept as planar Q8_0, an FP8 KV cache, one RTX 3090), placement stops being the problem and the token's launches become it. Every kernel launch costs 2-3 µs even when it does almost nothing, and a lone warp on an SM runs at an IPC of 0.2-0.3, so a kernel shaped as "one block of 4 warps per SM" is bound by latency, not by memory.

| Change | Before → after | Note |
|---|---|---|
| Resolve the router's picks on the device (a table of expert pointers), read the position from the device, replay the token as one CUDA graph | llama.cpp b12590 57 tok/s → 112 tok/s | the token needs nothing from the host between embedding and logits |
| Fuse launches: short convs into the products before them, routing into 1 launch, a looped layer's 3 norms and 4 messages into 1 each | 1,404 → 781 launches a token | each fusion bit-exact against the launches it replaces |
| Token attention on the tensor cores, a warp per run of keys, K and V straight from the cache into registers (no shared memory) | 65K deep: FP16 cache 63 → 77 tok/s, FP8 cache 53 → 84 | 846 GB/s over FP16, 776 GB/s over FP8; the FP8 cache becomes the faster one at depth |
| Load 4 attention-residual states at once in a thread, sum in the original order | 118.4 → 121.2 tok/s | bit-exact |
| All 12 core layers' loop messages of a pass in 1 launch (the bank does not change within a pass) | 127.2 → 128.2 tok/s | bit-exact, 24 launches less a token |
| The image encoder on the CPU | context 262,144 → 344,064 with images, peak 23.57 GiB at 340K | a new image takes ~4.5 s on 48 EPYC cores, the same image again 0.5 s |

What did not pay: skipping V reads where all of a step's attention weights are tiny (the model's global attention is spread out: 0.0% of 16-key steps were skippable on a real 64K text); grouping loads inside the loop-gate and FP16 matvec kernels (slower: the FP16 router matvec went from 4.9 to 24 µs); asking for more bytes ahead in the planar Q8 matvec (0%, it already reads 715 GB/s).

Profile after these (4K deep, FP8 cache, graph off): 8.34 ms of kernels a token, 781 launches. Dense Q8 2.54 ms (head ~0.6), NVFP4 experts 2.29, Q8 experts of layers 20-23 0.46, attention residuals 0.75, attention 0.66, loop gates 0.53. Weights read a token: 3.7 GB, so the memory floor is about 4.6 ms.

## Transfer checklist

1. Record picks on your real load, prefill and decode separately.
2. Validate the profile on a held-out part by time at your VRAM budget, against the ideal and against stock.
3. Compare with a random set of the same size. If the gap is under 10 points, rely on the live cache.
4. Freeze a copy of the profile per A/B arm, and confirm the profile is saved the way your service really stops.
5. Put every input type of the real load into the profile.

## Placement by profile is not pruning by profile

Pick frequency is a good metric for **where to keep** an expert and a bad metric for **how important** it is. For GLM the correlation of pick frequency with an honest importance score (REAP: router weight × norm of the expert output) was -0.22. Frequency is fine for placement and wrong for removal. Details in [10](10-pruning-and-merging.md).
