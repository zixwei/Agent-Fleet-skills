---
name: tls-configuration-review
description: Review the TLS posture of explicitly authorized services, including protocol versions, cipher configuration, certificate validity, hostname matching and common transport-security settings.
---

# TLS configuration review

## Purpose

Use this skill to review encryption in transit for approved hosts. It focuses on configuration and certificate analysis, not exploitation or traffic interception.

## Prerequisites

- Written authorization covers the named hostnames and ports.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The service owner has approved the testing window and any external TLS test service.
- Hostname, port, SNI name and expected certificate identity are known.

## Tools

- **testssl.sh:** [official project and usage](https://testssl.sh/).
- **SSLyze:** [documentation](https://github.com/nabla-c0d3/sslyze).
- **OpenSSL:** [official documentation](https://docs.openssl.org/).

## Workflow

### 1. Confirm the endpoint

Check the exact hostname and IP/port against the approved scope. Do not test a shared CDN, load balancer, mail gateway or third-party endpoint unless it is explicitly in scope. Use the correct hostname for SNI.

### 2. Inspect the certificate

Review subject alternative names, hostname matching, issuer, validity period, chain completeness, signature algorithm and key size. Confirm the presented certificate belongs to the intended service. Do not request, export or attempt to obtain private keys.

### 3. Review protocol and cipher configuration

Use one approved tool with conservative settings. For example:

```bash
sslyze app.example.test:443
```

Or:

```bash
openssl s_client -connect app.example.test:443 -servername app.example.test -brief
```

These commands connect to the specified service. Only run them when the host and port are authorized. Save output in the engagement's restricted evidence directory and redact internal details before sharing.

### 4. Review application transport behavior

For approved web applications, verify HTTPS redirects, HSTS behavior, secure cookie flags and mixed-content issues through normal application navigation. Consider proxy and load-balancer termination when interpreting results.

### 5. Validate and prioritize

Distinguish protocol support from actual negotiated connections. Record the exact hostname, port, SNI, tool version, timestamp, protocol and relevant configuration. Check recommendations against current upstream guidance and the service's compatibility requirements.

## Output

A TLS posture summary including certificate observations, protocol and cipher findings, evidence, compatibility impact and remediation guidance.

## Safety constraints

- Do not perform interception-in-the-middle against users or unrelated networks.
- Do not downgrade, disrupt or exhaust service connections.
- Do not scan discovered names or ports unless separately approved.
- Do not expose certificates or internal metadata beyond what the report requires.
- Stop if the endpoint is outside scope or the service owner asks to stop.

## References

- [testssl.sh](https://testssl.sh/)
- [SSLyze](https://github.com/nabla-c0d3/sslyze)
- [OpenSSL s_client](https://docs.openssl.org/3.0/man1/openssl-s_client/)
- [OWASP Transport Layer Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Security_Cheat_Sheet.html)
