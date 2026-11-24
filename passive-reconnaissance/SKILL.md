---
name: passive-reconnaissance
description: Organize low-impact asset discovery for explicitly authorized domains, validate every candidate against scope, and build a reviewed target inventory before active testing.
---

# Passive reconnaissance

## Purpose

Use this skill to build an initial asset inventory for an approved engagement. Prefer passive sources and keep discovered candidates separate from targets that have been approved for active testing.

## Prerequisites

- Complete [engagement scoping](../engagement-scoping/SKILL.md).
- Written permission covers the domain and any external data providers used for passive collection.
- A private working directory exists for engagement outputs.
- The operator has approved any active DNS or HTTP verification.

## Tools

- **Subfinder:** passive subdomain discovery — [documentation](https://docs.projectdiscovery.io/tools/subfinder/overview).
- **Assetfinder:** alternative passive asset discovery — [repository](https://github.com/tomnomnom/assetfinder).
- **dnsx:** DNS resolution and record inspection — [documentation](https://docs.projectdiscovery.io/tools/dnsx/overview).
- **httpx (ProjectDiscovery):** HTTP service metadata — [documentation](https://docs.projectdiscovery.io/tools/httpx/overview).

Install tools only from their official sources. Record their versions in the engagement notes. Some passive providers receive the queried domain; confirm that this disclosure is allowed by the owner.

## Workflow

### 1. Start from approved root domains

Create an input file containing only approved root domains. Do not seed from names found in unrelated websites, public certificate records or other organizations unless those sources and resulting assets are covered by the authorization.

Check each root domain with the local scope guard:

```bash
python scripts/scope_guard.py --scope engagement.json --target example.test
```

### 2. Collect passive candidates

For one approved domain:

```bash
subfinder -d example.test -silent -o candidates.txt
```

Treat every result as an unverified candidate, not as permission to scan. Keep the raw output private. Deduplicate and normalize hostnames before review.

### 3. Review each candidate against scope

For each candidate, check the exact hostname:

```bash
python scripts/scope_guard.py --scope engagement.json --target candidate.example.test
```

Remove blocked, ambiguous, third-party and explicitly excluded hosts. Never automatically expand the allowlist based on tool output. Ask the owner to approve newly discovered assets before active verification.

### 4. Resolve only approved hostnames when permitted

DNS queries create traffic and may disclose information to a resolver. Obtain approval for the resolver and query types, then run a low-rate check against a reviewed list:

```bash
dnsx -l approved-hosts.txt -a -aaaa -cname -resp -rate-limit 2 -o dns-records.txt
```

Review CNAME destinations and shared hosting carefully. A CNAME pointing to a provider or another organization does not authorize testing that provider.

### 5. Collect minimal HTTP metadata when permitted

HTTP metadata checks send requests. Run them only against hostnames separately approved for HTTP verification. Do not follow redirects automatically; inspect redirect destinations and re-check scope first.

```bash
httpx -l approved-http-hosts.txt -status-code -title -tech-detect -rate-limit 2 -timeout 5 -retries 0 -o http-inventory.txt
```

Use the output to inventory status codes, titles and broad technology hints. Do not treat a banner or technology guess as proof of a vulnerable version.

### 6. Produce a reviewed inventory

Maintain columns for hostname, source, discovery date, scope decision, DNS result, HTTP result, owner confirmation and notes. Mark each asset as one of:

- **Approved:** explicitly covered and checked against the scope file.
- **Candidate:** discovered but not yet authorized for active testing.
- **Excluded:** out of scope or explicitly denied.
- **Unresolved:** ownership or authorization requires clarification.

Only assets marked Approved may be passed to later active-testing skills.

## Output

A deduplicated, reviewed asset inventory with raw candidate data stored separately and access-controlled. Include tool versions, collection time, sources, resolver and limitations.

## Stop conditions

Stop if a tool returns out-of-scope assets, redirects to third-party hosts, triggers blocking or instability, or if provider terms and data disclosure requirements are unclear. Do not use brute-force wordlists, mass internet-wide search, stealth, evasion or high-rate enumeration as part of this workflow.

## References

- [ProjectDiscovery Subfinder](https://docs.projectdiscovery.io/tools/subfinder/overview)
- [ProjectDiscovery dnsx](https://docs.projectdiscovery.io/tools/dnsx/overview)
- [ProjectDiscovery httpx](https://docs.projectdiscovery.io/tools/httpx/overview)
- [OWASP Web Security Testing Guide — Information Gathering](https://owasp.org/www-project-web-security-testing-guide/)
