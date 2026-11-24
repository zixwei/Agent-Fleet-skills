---
name: sim-guard
description: Guardrails for AI agents that use iOS simulators, so parallel agents don't melt the Mac. Use before any session boots, builds to, runs on, taps through or screenshots an iOS simulator (Xcode MCP, xcodebuild, simctl, XcodeBuildMCP, ios-simulator-mcp, Maestro), and when the user asks why the Mac is hot, slow or out of memory.
---

# sim-guard

A stock iOS simulator idles at about 3.5 GB and 130+ processes. Five agents booting their own sims will take a 32 GB Mac down. These rules keep it to a small pool of slim sims.

The script is `sim-guard.sh` in this folder. It needs `simslim` (`brew install mobai-app/tap/simslim`).

## First time on a machine

Run `sim-guard.sh init`. It picks 2 iPhone sims on the newest iOS runtime, writes them to `~/.config/sim-guard/pool`, and slims them with simslim. Slim sims keep StoreKit, push and universal links on, so paywalls and deep links still work.

## Rules for every agent

1. **Preflight first.** Before building to or booting a simulator, run `sim-guard.sh preflight`. Exit 1 means blocked. Don't boot anything.
2. **Hard cap of 2 booted sims machine-wide** (`SIM_CAP` changes it). Preflight also blocks when Mac memory free is under 25%. When blocked, wait, reuse a pool sim this session already owns, or ask the user. Never boot a third.
3. **Use the pool, never create sims.** Get one with `sim-guard.sh acquire`. It prints `ACQUIRED <udid>`. Pass that UDID everywhere: xcodebuild `-destination id=<udid>`, the Xcode MCP run destination, simulator MCP tools. Never `simctl create` or clone a sim for a task. Don't `simctl erase` either, it can drop the slim profile.
4. **Release after every run.** `sim-guard.sh release <udid>` removes user-installed apps, resets the keychain and shuts the sim down. Add `--keep-apps` only when the user wants to keep using the app.
5. **Orphans.** Any booted sim outside the pool is an orphan. Audit prints the shutdown command. Run it only when you're sure no other live agent is using that sim. Sims carry no owner tag, so ask the user if unsure.
6. **One agent per sim.** Two agents tapping the same sim corrupt each other's runs.

## Auditing

`sim-guard.sh audit` lists every booted sim with UDID, name, runtime, RAM, CPU, slim or not, pool or orphan, plus Mac memory free. `simslim top` is the live view.

Prompt to give any agent: "Run `sim-guard.sh audit`. Report count, UDIDs, names, runtimes, RAM and slim state. Flag orphans and give the shutdown commands, but don't run them."

## Overnight or multi-agent jobs

The orchestrator runs `sim-guard.sh audit` between waves and holds the next wave while preflight is blocked. That gets you the "monitor RAM and tell agents to free sims" behaviour without paying for an always-on agent.
