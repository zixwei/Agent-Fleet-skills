---
name: engagement-scoping
description: Establish and verify written authorization, target allowlists, exclusions, test limits, and stop conditions before any security assessment begins.
---

# Engagement scoping

## Purpose

Use this skill at the beginning of every assessment and whenever scope, authorization, test methods, dates, or ownership change. This is the mandatory first skill in Pentestizer. No active testing skill may proceed until the operator has completed the scope and authorization checks.

## Required inputs

- A current written authorization from the system owner.
- The engagement rules of engagement, including the approved test window.
- Exact in-scope domains, hostnames, IP addresses, CIDR ranges, applications, APIs and repositories.
- Explicit exclusions and third-party dependencies.
- Allowed techniques, source IPs, request limits and authenticated test permissions.
- Emergency contact and stop procedure.
- Approved evidence storage and retention rules.

Missing or ambiguous authorization is a stop condition. Do not infer permission from public access, domain ownership, a disclosure policy, or a bug bounty program's existence.

## Workflow

### 1. Read the authorization

Confirm who granted permission, that they are authorized to grant it, which legal entity and assets are covered, and when permission starts and expires. Confirm that the requested test type is explicitly allowed. Ask the operator to resolve any ambiguity with the asset owner.

### 2. Build an explicit allowlist

Create a private JSON file based on the schema in [the example engagement scope](../../examples/engagement.example.json). Populate:

- `allowed_domains`: exact domains and their subdomains that are covered.
- `allowed_ips`: individually authorized IP addresses.
- `allowed_cidrs`: explicitly approved address ranges.
- `excluded_targets`: exact hosts, domains, addresses or CIDRs that must never be tested.

Use the narrowest scope possible. Do not add related domains, discovered assets, CDN addresses, cloud provider ranges, redirects, third-party APIs or adjacent IPs unless the owner explicitly authorizes them.

### 3. Check a proposed target locally

Run the helper for every target before giving it to an active tool:

```bash
python scripts/scope_guard.py --scope /secure/path/engagement.json --target https://app.example.test
```

The helper returns `ALLOWED` only when the target matches an allowlisted domain, IP or CIDR and does not match an exclusion. A non-zero result means stop and resolve the scope. Do not edit the allowlist merely to make a target pass.

The helper checks only the host/IP identity. It does not verify the authorization document, resolve DNS, inspect ownership, validate permitted ports, protocols, paths, dates or techniques, validate redirects, or enforce scope inside external tools. Check every other rule-of-engagement limit separately. It is an offline convenience check, not a security boundary.

### 4. Define safe operating limits

Record approved limits for each technique. At minimum, define:

- Maximum requests per second and concurrency.
- Permitted ports, protocols, paths and API environments.
- Whether authenticated tests and test accounts are approved.
- Whether fuzzing, exploit validation or state-changing tests are approved.
- Explicit exclusions for denial-of-service, stress, destructive and social-engineering tests unless a separate plan exists.

Default to passive, read-only, low-rate activity. Human approval is required before authenticated testing or any action that may alter state or affect availability.

### 5. Prepare a stop plan

Stop immediately and notify the owner if authorization expires or is withdrawn, a target is not clearly in scope, an out-of-scope redirect or dependency appears, service instability occurs, sensitive production data is exposed, or the owner asks to stop.

### 6. Record the scope decision

Produce a short scope summary containing the authorization reference, approved assets, exclusions, allowed methods, time window, rate limits, emergency contact and unresolved limitations. Do not copy confidential authorization documents into a public repository.

## Output

A scope-approved engagement record and an explicit list of target identifiers that downstream skills may use. If authorization or scope cannot be established, the only permitted output is a request for clarification or a non-invasive planning document.

## Safety rules

- Never broaden scope based on reconnaissance results.
- Never treat a subdomain match as permission to test a third-party hosted service.
- Check every redirect destination and newly discovered IP independently.
- Never place credentials, client names or private target data in committed examples.
- Use exclusions as deny rules; exclusions always take precedence over allow rules.

## References

- [Pentestizer Security Policy](../../SECURITY.md)
- [Rules of Engagement template](../../templates/rules-of-engagement.md)
- [Agent Skills specification](https://agentskills.io/specification)
