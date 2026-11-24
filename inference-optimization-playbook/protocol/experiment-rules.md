# Experiment rules

One page of hard rules for any speed experiment. The reasons and the cases behind them are in [14 Benchmarking and traps](../playbook/14-benchmarking-and-traps.md) and [15 Quality and release gates](../playbook/15-quality-and-release-gates.md).

The playbook is a map, so the order of experiments is yours to choose. These rules hold on any route.

## Before the run

1. **Know the ceiling.** Compute what the metric can reach at best ([01 Cost model](../playbook/01-cost-model.md)). If the target is above it, say so to the user with the arithmetic before you start.
2. **Check the ledger.** If the idea has a row, read its "why" and "revisit when". Rerun a rejected idea only with a reason that the row does not cover.
3. **Write the threshold down.** State the metric, the expected effect and the smallest change you will accept. First take your own A vs A spread. Ours on a replay of real sessions: 0.5% on the session sum, 1.5% on the median read, 5% on decode.
4. **State the quality level.** Bit-exact, a tolerance on logits, or behavior only. Chunk size, quant, placement, exchange packing and drafter acceptance are numeric changes until shown otherwise.
5. **Freeze the control.** Weights, engine revision, launch arguments, KV precision, a frozen copy of the expert profile, cache states, placement of experts, input IDs and seed.

## During the run

6. **One change per experiment.** Derive it from the current best config. If you had to change a bundle, record the total and label which ablations exist.
7. **Controls on both sides.** Run control, candidate, control (or ABAB) in one session and compare the candidate only with its neighbors. Results taken hours apart or on another RAM config do not compare.
8. **Decide on real load.** A replay of real sessions and free generation decide. A short test is for debugging: it promised +27% where real generation gave +2%.
9. **Keep the box quiet.** No other GPU jobs, no monitoring tool polling the driver, no orphan server left on the port by an interrupted run. Record the power limit. Run STREAM Triad before and after: if bandwidth dropped, the series does not count.
10. **Confirm the fast path ran.** After every start, find in the log that the feature under test is active. EP, a drafter and session snapshots have all turned off silently here.
11. **Take the timings from the engine,** tokens from the server's usage stats. One SSE chunk can carry several tokens.

## After the run

12. **Accept only above the threshold and with nothing else broken.** Report the mean, the worst block, the worst full answer and the worst rolling window. A candidate that raised one minimum and lowered the mean was rejected here.
13. **Run the quality gate you stated,** and always a second turn with a snapshot in between ([15](../playbook/15-quality-and-release-gates.md)).
14. **Write it down, the failures too.** A dated entry by the [template](entry-template.md), 1 row in the [ledger](../ledger/hypotheses.md) with tags, a pointer in the model's case file.
15. **Stop on a plateau.** After 3 changes in a row inside the noise, stop and write a status: best numbers against the baseline and the ceiling, what limits further gains with evidence, and the remaining ideas ranked by cost. Then profile before the next attempt.

## On a shared machine

- Run 1 GPU job at a time.
- Find processes by PID. A pattern kill can match your own shell.
- Let requests drain before you stop a server.
- Detach long runs so they survive a restart of the agent, and watch them by their log.
- Restore everything you changed (power limit, NUMA settings, services you stopped) and tell the user what state the box is in.

## The report to the user

Start with a table:

| Metric | Baseline | Now | Ceiling | How it was taken |
|---|---:|---:|---:|---|
| Decode, short and deep context | | | | free generation, content, sampling, series size |
| Prefill, by input length | | | | cold unique input, new tokens only |
| Re-read of a typical turn | | | | replay of real turns |
| TTFT at the client | | | | |
| Load | | | | cold or warm page cache |

Then, in this order:

1. What gave the gains, 1 line each with its number.
2. What limits further gains, with evidence: the fit, a profile, the share of the ceiling reached.
3. What was tried and rejected, and why.
4. What was not verified.
5. What state you left the machine in.

Use medians, not best runs. Label single runs, simulations and calculations. Do not put numbers from different harnesses in one column.
