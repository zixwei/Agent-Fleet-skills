# Tools

Two small scripts that answer orientation questions from the [situation card](../protocol/situation-card.md). Standard-library Python, no install. They are instruments for finding your place on the map, not a benchmark harness.

## route_sim.py: is routing skewed, and what can a cache give

```bash
python3 tools/route_sim.py trace.jsonl --fraction 0.5
python3 tools/route_sim.py trace.jsonl --slots 64 --phase decode
python3 tools/route_sim.py --demo skewed --fraction 0.3
python3 tools/route_sim.py --demo uniform --fraction 0.3
```

Input is a router trace in JSON Lines, 1 line per (token, layer):

```json
{"t": 0, "layer": 0, "experts": [12, 45, 301, 7], "phase": "decode"}
```

`t` is the token index in processing order. `phase` is optional. Most engines can log the router picks with a debug flag or a few lines of code. Record 5-10 thousand tokens of your real load, prefill and decode separately.

The first half of the tokens builds the profile, the second half is the test. For every layer the script prints the effective number of experts and the hit rate of 6 policies: a random set, a static set by profile, the best static set for the test part, LRU, a decaying heat policy, and Belady (the best possible cache, which needs the future).

How to read it:

| What you see | What it means | Where to go |
|---|---|---|
| static is far above random | routing is skewed, a profile is worth having | [03](../playbook/03-expert-profile-and-placement.md) |
| static is within 10 points of random | weak skew, the live cache is the main mechanism | [04](../playbook/04-live-expert-cache.md) |
| oracle is far above static | every part of the load has its own working set, or the profile is from other traffic | [03](../playbook/03-expert-profile-and-placement.md), validation |
| belady is far above heat | the cache policy has headroom. Try a shorter decay (`--decay 0.5 --decay-every 50`), more moves (`--swaps 4`) | [04](../playbook/04-live-expert-cache.md) |
| lru has more hits and many more moves per token | count the cost of a move before you choose it | [04](../playbook/04-live-expert-cache.md) |

The 10 point threshold is a heuristic. The policies are generic models. A real engine has a global move budget, async copies and admission rules, so use the output for the size of the effect and confirm on the engine.

## fit_cost.py: which term of token time is larger

```bash
python3 tools/fit_cost.py runs.csv --y ms --x misses
python3 tools/fit_cost.py rounds.csv --y ms --x rows,rows_x_miss
python3 tools/fit_cost.py --demo
```

Input is a CSV with a header. For the token model take 3 or more runs with different VRAM budgets for experts and record misses per token and milliseconds per token:

```csv
misses,ms
60,40.2
45,32.4
30,25.1
15,17.4
```

It fits `ms = a + b × misses`, prints R², the ceiling with no misses (`1000 / a` per second), the share of the miss term in every run, and what a cut in misses would give.

For a speculative round, record rows of the verify window and `rows × miss rate` and fit both regressors.

If the miss term is small, stop working on placement and go to the constant part ([01](../playbook/01-cost-model.md)).

## Rules

The demo data is synthetic. Do not quote it as a result. See [AGENTS.md](../AGENTS.md) for how tools are added.
