---
name: network-service-enumeration
description: Inventory ports and service metadata on explicitly authorized IP addresses using conservative Nmap settings and a bounded target and port list.
---

# Network service enumeration

## Purpose

Use this skill to understand the network services exposed by a known, approved set of hosts. It is not intended for internet-wide scanning, stealth, evasion or exploitation.

## Prerequisites

- Written authorization explicitly allows network scanning of the exact IP addresses.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Approved IPs are listed in a reviewed input file.
- The owner has approved the port list, scan window and rate.
- A contact is available if availability or monitoring alerts occur.

## Tool

**Nmap** — [Reference Guide](https://nmap.org/book/man.html) and [official documentation](https://nmap.org/docs.html). Record the Nmap version and execution host.

## Workflow

### 1. Confirm every target

Use literal approved IP addresses where possible. Do not scan an entire subnet merely because one address is in scope. If a host resolves to an IP address, confirm that the address itself is covered. Check redirects, NAT, load balancers and shared infrastructure with the owner.

### 2. Define a minimal port list

Start with ports identified by the owner or application architecture. Avoid scanning all 65,535 ports by default. A limited example for a host explicitly authorized for these ports is:

```text
22
80
443
```

### 3. Run conservative service detection

For a reviewed IP list and an approved set of ports:

```bash
nmap -sT -sV --version-light -T2 --max-retries 1 --max-rate 10 \
  --host-timeout 2m -p 22,80,443 -iL approved-ips.txt \
  -oA evidence/nmap-services
```

This sends traffic to each named IP and port. Confirm the exact command and limits with the owner before execution. Reduce rate or stop if monitoring alerts, service degradation or blocking occurs.

### 4. Review results

Classify ports as open, closed or filtered. Treat service names and version banners as indicators, not definitive software identification. Confirm important details through owner-provided inventory or authenticated host review.

### 5. Decide whether follow-up is permitted

Do not automatically run NSE scripts, exploit checks, credential tests or intrusive probes. Review the specific script or check and obtain explicit approval for its behavior. If additional ports or hosts are needed, obtain an updated authorization and scope file first.

## Output

A concise service inventory containing IP, port, state, service guess, version string, timestamp, Nmap version, scan options and limitations. Redact internal hostnames and sensitive network details before sharing externally.

## Safety constraints

- Never use spoofing, decoys, fragmentation, proxy relays or evasion options.
- Do not run denial-of-service, stress, brute-force or exploit scripts.
- Do not use broad CIDR or all-port scanning unless the authorization explicitly lists that exact range and scan plan.
- Do not treat filtered ports or absent responses as proof that a service is secure.
- Stop immediately on instability or a request from the owner.

## References

- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Nmap Usage and Legal Notes](https://nmap.org/book/legal-issues.html)
