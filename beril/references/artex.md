# ARTEX

Repo: https://github.com/BaaRai/ARTEX  
Upstream: `Autumn-27/ARTEX`  
License: AGPL-3.0  
Language: Go  
Stars: 15.5k at collection time

ARTEX is a self-hosted, LLM-driven penetration testing platform. A planner splits a goal into intents, workers run real tools, and results are written into two linked graphs. It is a runner, not a knowledge base.

## Architecture

- **Planner and workers.** The planner decomposes a goal. Workers execute one intent with a tool. Results feed back into the loop.
- **Two graphs.** The asset graph records what exists. The exploration graph records what was tried and what it produced. Anchors link them.
- **Evidence.** A recording MITM proxy and browser extension capture requests and responses. Findings and retests bind to the exact traffic.
- **Brakes.** Scope and deny rules, approval gates, argv-prefix rules, token and turn budgets, and a target noise governor.
- **Prompt-injection framing.** Tool output is framed as untrusted before it reaches the model.
- **Providers.** Anthropic, OpenAI-compatible, or local Ollama. Worker rotation across profiles.

## Install

```bash
git clone https://github.com/BaaRai/ARTEX.git
cd ARTEX
cp .env.example .env
# set POSTGRES_PASSWORD and optionally an LLM key
docker compose up -d --build
```

Open `http://localhost:8787`. The first visit asks for a one-time setup token:

```bash
docker compose logs artex | grep "Setup token"
```

Release images are at `ghcr.io/baarai/artex` for amd64 and arm64.

## Configure before a task

1. System -> LLM: add the provider profiles.
2. System -> Settings: set worker count to at least 2 if you want rotation.
3. System -> Agents -> Worker: leave the model on follow-task, or pin one.
4. Create a task, add both profiles, enable rotation.
5. Set scope and deny rules before start.
6. Set the token and turn budget.
7. Enable approval gates for writes.

## Where it fits

| Job | ARTEX | Beril |
|---|---|---|
| Goal decomposition and tool execution | Yes | No |
| Evidence-bound findings | Yes | Links and snapshots |
| Asset graph | Yes | Catalog rows |
| Method cards and reading | Thin | Deep |
| Local artifact scan | Limited | `scan`, `re` |
| Training | No | 25 weeks and labs |

Use ARTEX to run a broad autonomous pass. Use Beril to choose the class, verify the primitive, and write the report. Do not run both against the same host at full rate without a shared rate budget.

## Safety

- Start with a lab target.
- Read the scope gate and argv rules. A worker can run any tool in the allowlist.
- Set a token and turn budget. Loops are the main cost risk.
- Keep approval on for writes, uploads, and account creation.
- The recording proxy generates traffic. Check the target's rate limits.
- AGPL means changes to a hosted version should be published. Check with counsel before a commercial fork.

## Limits

- It finds and records; it does not replace a human triager.
- LLM plans can repeat or wander. The budgets are the brake.
- Coverage depends on the installed tools.
- Prompt-injection framing lowers risk, not to zero.
- Current CVE chains in the prompt age.

## Sources

- ARTEX README and `SECURITY.md`
- `docker-compose.yml` and the agent prompts in `agent/`
- Upstream `Autumn-27/ARTEX`
