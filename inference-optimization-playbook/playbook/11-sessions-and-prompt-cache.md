# Sessions, KV and the prompt cache in agent work

Covers DeepSeek V4.1 Flash, MiniMax-M3.1-Flash, Qwen3.8-Flash-Next, Qwen3.5-122B, Hy4 Flash.

> **Read when:** the load is a coding agent or a multi-turn chat. **Skip when:** the load is single-shot batch jobs. **Ledger tags:** `#agent` `#kv`.

## What an agent turn is made of

A live DeepSeek session (295 turns, context up to 279K):

- generation 2,903 s, reading 884 s: **77% / 23%** (81% / 19% on turns above 100K);
- new prompt tokens per turn: median 243, not 4-8K as we thought before;
- 263 of 294 turns continue exactly from `prompt + answer`;
- 4 reads from scratch took 24% of all reading. 2 of them were probably caused by the client changing the start of the prompt.

MiniMax on a replay of real sessions looked the opposite: 87% of time in reading. The cause was slow attention ([07](07-prefill.md)). After the fix the full session got 3.75x faster.

So in agent work the time of a turn is generation and re-read, not cold prefill. The prompt cache and session persistence pay more than speeding up prefill at 128K.

## What pays

| What | Effect |
|---|---|
| Prompt cache in the engine (standard) | appending 2K to 200K: 3.5-3.9 s vs 121 s from scratch (exllamav3) |
| A change at the start of the prompt (system prefix) | 0% in cache, 157 s again. The main enemy of the cache in agents |
| Session files (Strata, a patch for saving slots under layer split) | 120K: restore 7.2 s instead of 34.7 s of cold prefill, the answer is byte for byte. At 55K save 0.5 s, restore 0.7 s |
| Conversation slots in RAM (DeepSeek) | a 32K conversation comes back in 3.7 s instead of ~20 |
| Incremental prompt prep (DeepSeek, server) | the first SSE event cost 1.2-2 s: a special-token regex walked the full prompt every turn. After the fix ~138 ms, turns -14.7% together with RAM copies of borrowed experts |
| Warming the prefix while the person types (Qwen3.5-122B) | TTFT at 8K 4.63 → 0.16 s |

A session file is tied to the engine version, the config and the number of cards: one saved on 2 cards will not restore on 1 or 3.

A trap of hybrid models (DeltaNet/GDN): under sampling the model sometimes emits a non-canonical token split (~1 in 1,000), re-tokenizing the history diverges, and a hybrid cache can only roll back to a checkpoint. The next turn then re-read 30-54K tokens. The fix: compare the common prefix by text, not by tokens.

## Dialog state with several agents (Hy4 Flash)

**The problem.** A repeat A → A is easy to mistake for multi-agent support. With A → B → A, the state of A may already be evicted. On Hy4 a snapshot has to keep in agreement the KV, the sliding window, the MTP state and the position of the last accepted token. With vision, the identity of the input on the image segment also changes.

| Test | Result | What to check |
|---|---|---|
| 2 dialog snapshots in pinned RAM | Returning to A after an independent B restored 26,624 of 27,839 tokens. External TTFT fell from 9.617 to 0.477 s. Creating a large snapshot the first time cost about 0.24 s | The cost of creation, hits and restore, and a continuation with a changed suffix |
| Allocate snapshot memory before the server is ready | Lazy allocation dropped 1 cold block to 2,314.12 tok/s. With a reserve of 6.394 GiB of pinned RAM made in advance, preparation took 1.35 s and all 128 blocks of the new test passed above 2,500 | Count service start, the first large request and the time steps at snapshot boundaries |
| Tie the snapshot grid to the real prefill block | After changing 2,048 to 4,096 the server on 2 GPUs silently stopped saving the needed snapshots. After the fix a return to 44.6K went from 35.8 to 4.1 s, with 40,960 restored | Check the counter of restored tokens at every working block size |
| A short request during a long prefill | A test with priority for the short request cut its TTFT from 8.50 to 1.51 s. For a warmed history of 37,288 tokens: from 21.74 to 1.70 s. The scheduler was tested with the short answer capped at 256 tokens | Also time the delay of the long request, the queue, cancellation, SSE correctness and state preservation. This is a separate scheduling effect |

**Acceptance in the application.** 2 sessions got different tasks with different control strings. Checked: a real tool call, the ID and the return of the result, the final answer after someone else's request, a separate reasoning channel, a repeat with the same seed, and a change of image. The 2 sessions overlapped for 25.385 s, with 1 request waiting in the queue. On 1 card in another series, a return to a history of 94,536 tokens restored 90,112 and took 6.37 s vs 70.43 s for the original request. Both numbers are full request time.

## Transfer checklist

1. Log a real agent session first: generation vs reading time, new tokens per turn, how many turns continue from `prompt + answer`, how many read from scratch and why.
2. Find what invalidates the prefix in your client (timestamps, reordered tools, a changed system prompt). Fix that before any engine work.
3. Test A → B → A. A repeat of A → A proves little. Read the counter of restored tokens together with the wall time.
4. After any change of the prefill block size, check that snapshots still save and restore.
5. Compare the common prefix by text if the model can emit non-canonical tokenization.
6. Time the first SSE event separately from the engine timings. Server-side prompt prep can cost seconds.
