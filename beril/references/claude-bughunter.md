# Claude-BugHunter

Repo: https://github.com/elementalsouls/Claude-BugHunter  
License: MIT  
Stars: 16.7k at collection time  
Author: Sachin Sharma

A Claude Code skill bundle for bug bounty and external red team work. It ships 83 skills, 15 slash commands, and 681 disclosed-report patterns across 24 vulnerability classes. It is a companion to Beril, not a replacement.

## What it adds

- A five-phase hunting workflow and a red-team operator mindset.
- Per-class hunt skills with detection patterns, bypass tables, and chain templates.
- Enterprise identity and edge chains: M365, Entra, Okta, vCenter, SSL-VPN, SharePoint, cloud IAM.
- A 7-Question Gate, VRT-aware severity, OOS rebuttals, and evidence hygiene.
- Engagement scaffolding and a Burp MCP integration.
- An optional HackerOne MCP for private scope and duplicate search.

## Install

Plugin path, inside Claude Code:

```text
/plugin marketplace add elementalsouls/Claude-BugHunter
/plugin install claude-bughunter@elementalsouls
```

Copy path:

```bash
git clone https://github.com/elementalsouls/Claude-BugHunter
cd Claude-BugHunter
bash scripts/install.sh
```

Windows:

```powershell
pwsh ./scripts/install.ps1
```

The copy path writes into `~/.claude/`. The plugin path keeps the bundle namespaced and updatable.

## Skill map

The 83 skills group into:

| Group | Examples |
|---|---|
| Method | `bb-methodology`, `redteam-mindset`, `recon-scope-triage` |
| Web classes | `hunt-sqli`, `hunt-xss`, `hunt-ssrf`, `hunt-idor`, `hunt-ato`, `hunt-race-condition` |
| Parser and protocol | `hunt-http-smuggling`, `hunt-cache-poison`, `hunt-websocket`, `hunt-grpc` |
| Identity | `hunt-oauth`, `hunt-saml`, `hunt-jwt-crypto`, `hunt-mfa-bypass`, `hunt-session` |
| Platform | `hunt-nextjs`, `hunt-nodejs`, `hunt-laravel`, `hunt-springboot`, `hunt-aspnet`, `hunt-sharepoint` |
| Cloud and infra | `hunt-cloud-misconfig`, `hunt-k8s`, `cloud-iam-deep`, `enterprise-vpn-attack` |
| AI | `hunt-llm-ai`, `hunt-rag-vector` |
| Mobile | `apk-redteam-pipeline`, `ios-redteam-pipeline` |
| Report | `triage-validation`, `evidence-hygiene`, `report-writing`, `bugcrowd-reporting` |

Commands: `autopilot`, `chain`, `hunt`, `intel`, `memory-gc`, `pickup`, `recon`, `remember`, `report`, `scope`, `surface`, `token-scan`, `triage`, `validate`, `web3-audit`.

## The 7-Question Gate

From `triage-validation`. A finding must answer:

1. Can an attacker do this now?
2. Does it cross a real trust boundary?
3. Is the impact money, PII, account takeover, or code execution?
4. Is the precondition realistic?
5. Is the evidence reproducible?
6. Is it already public or intended?
7. Would a triager accept the severity?

A no on question 1 or 3 kills the report until the chain is stronger.

## Evidence hygiene

- Mask tokens, cookies, and PII in screenshots.
- Keep the raw request and response, not only a picture.
- Redact the account, not the bug.
- Record the time, host, and build.
- Separate observation from inference.
- Store the evidence outside the report body and link it.

## Burp and HackerOne MCP

The bundle can drive a Burp MCP server for proxy work and a HackerOne MCP for scope and duplicate checks. Both are optional. Public skills work without an account.

## Use with Beril

1. Use Beril for the target map, source catalog, artifact scan, and evidence links.
2. Use Claude-BugHunter for the class-specific hunt pattern and the triage gate.
3. Use `references/techniques.md` from Beril to verify a primitive.
4. Use the 7-Question Gate before writing.
5. Put the final report through `docs/break-my-app.md`.

Do not install both into the same namespace. Keep Claude-BugHunter under `claude-bughunter:` and Beril at the project root.

## Limits

- The 681 patterns are curated from disclosed reports. 433 are individually cited in the bundle. Check the citation before quoting a pattern as precedent.
- Platform chains age with CVEs. Recheck the version.
- A skill is a checklist, not a scanner.
- The bundle assumes authorization. It does not grant scope.

## Safety

Read `SECURITY.md` in the repo. The install runs shell and PowerShell scripts. Read them before running in a sensitive home directory.
