# Protocol: a new model on your hardware

Before this checklist, fill the [situation card](situation-card.md) and read the [experiment rules](experiment-rules.md). The steps below are the ones that were useful on our models, in the order we would take them now. They are not a fixed sequence: skip what the [map](../playbook/00-start-here.md) routes you around.

Two parts. Part A sets up the experiment so the results mean something. Part B is the order of technical steps for an MoE that does not fit in VRAM. For a dense model skip B2-B7 and go to B8.

## Part A. Before the first optimization

1. **Describe the load and the criterion before the launch.** Number of concurrently active requests, the range of input and output lengths, content type, languages, sampling mode, the target minimum or mean, and the allowed quality shift. For total throughput use the sum of tokens divided by the sum of time over sequential requests. For concurrent load count total output over total wall time.
2. **Freeze the control.** Record the weights, the engine revision and parameters, the working context, KV precision, the template, input IDs, the placement, available RAM/VRAM and the warmup state. Prepare a short input, an independent long text, positions at the window edge and a second turn with a tool. Set part of the examples aside for the final check.
3. **Build the reference.** A slow correct path (host F32 or another engine) that gives intermediate values. Compare a normal step, batch prefill and multi-position verify separately ([14](../playbook/14-benchmarking-and-traps.md)).
4. **Check the physical explanation.** For offload count bytes per fresh token and profile the overlap. For exchange time the wait for neighbors. For a drafter count accepted tokens per millisecond of the full step. Turn the mechanism on and off inside one build when possible.
5. **Run the normal entry point.** Separate process loading from cold prefill of an already loaded model. Record client TTFT, the speed of new input tokens only, output time, the minimum over blocks and over output windows. Repeat after warmup and after a dialog eviction.
6. **Leave the decision with its revisit conditions.** Say what went into the working profile, what stayed as an alternative, what was removed, and which experiment could change the decision. If a bundle of optimizations changed, keep the total result and label the available ablations separately.

## Part B. An MoE that does not fit in VRAM

1. **Take the base of your hardware.** Triad by thread count, host-to-device bandwidth for 1 card and for all cards at once, P2P between cards. This already shows which is cheaper: a CPU expert or a PCIe expert.
2. **Record a router trace** on 5-10 thousand tokens of your load (prefill and decode separately). Compute the effective number of experts per layer and the curve "share of picks from top-N" ([01](../playbook/01-cost-model.md)).
3. **Count how many experts fit in VRAM** at the context, KV, buffers and drafter you need. Compute the expected misses per token from the curve in step 2.
4. **Build the time model** `a + misses × cost` and decide where to invest: VRAM for experts, RAM bandwidth, CPU code, or the quant.
5. **Accumulate the profile on real domain traffic** and validate it on a held-out part by time. If the domain has images, tables or other languages, they have to be in the profile ([03](../playbook/03-expert-profile-and-placement.md)).
6. **Turn on a live cache with decay** on top of the static placement. If skew is weak, this is the main thing ([04](../playbook/04-live-expert-cache.md)).
7. **A decode miss: compare the real cost of a CPU expert under load with the cost of a PCIe copy and the time of a layer.** For us the CPU won more often, but not always. Prefill: DMA from pinned RAM, large chunks, EP over several links when possible ([02](../playbook/02-serving-a-miss.md), [07](../playbook/07-prefill.md)).
8. **Layer split if experts are offloaded, TP if everything is in VRAM.** TP under partial placement with a CPU lane serving the missing experts worked on 1 model and 1 engine version (Hy4 Flash, Maya v1.1.69), so test it before you rely on it ([05](../playbook/05-multi-gpu.md)).
9. **Add a drafter,** set the confidence threshold, check the draft vocabulary for your language. On models with weak skew account for the window cost ([08](../playbook/08-speculative-decoding.md)).
10. **Fix the agent path:** prompt cache, what invalidates the prefix, snapshots for A → B → A ([11](../playbook/11-sessions-and-prompt-cache.md)).
11. **Benchmark on a replay of real sessions** and free generation, with a threshold written down in advance. A short test is for debugging only ([14](../playbook/14-benchmarking-and-traps.md)).
12. **By default do not cut experts while you can keep them in RAM.** If you do cut (for a narrow task), score importance with REAP or "router weight × input norm", not frequency, and check quality on every domain ([10](../playbook/10-pruning-and-merging.md)).
13. **Gate the release** on the 3 levels of quality and on the number the gate names ([15](../playbook/15-quality-and-release-gates.md)).

## The cheapest first tests

| Question | Test that takes an hour, not a week |
|---|---|
| Will a static hot set work? | hit simulation on a router trace: profile vs random at your budget |
| Is a better cache policy worth building? | simulator: your policy vs Belady on real traces |
| CPU lane or PCIe stream? | time 1 expert on the CPU under load, 1 expert copy, 1 layer |
| Will a drafter pay? | window cost fit from the engine + sequential window simulation on saved taps |
| Is there anything to win by training the drafter? | ceiling: a drafter that always outputs the model's argmax |
| Will more RAM channels help? | hit rate and the CPU share of a token. If the CPU computes little, no |
| Will pruning help speed? | only if it removes offload. Otherwise it saves memory, not time |
| Is my chunk change safe? | teacher-forced logits vs the old chunk on 500+ positions |
