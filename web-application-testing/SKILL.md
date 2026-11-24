---
name: web-application-testing
description: Perform a structured, low-impact manual assessment of authorized web applications using OWASP testing guidance, proxy tools, and reproducible evidence.
---

# Web application testing

## Purpose

Use this skill to assess an explicitly authorized web application. Structure the review around the OWASP Web Security Testing Guide (WSTG) and the application's actual threat model. Automated scanners support the work but do not replace manual analysis.

## Prerequisites

- Written authorization names the exact application, environment and permitted test methods.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The owner has provided test accounts for each required role, when authenticated testing is approved.
- Test data is synthetic and isolated from real customer records.
- The owner has confirmed rate limits, test windows and state-changing test permissions.

## Tools

- **Burp Suite:** [official documentation](https://portswigger.net/burp/documentation).
- **OWASP ZAP:** [user guide](https://www.zaproxy.org/docs/).
- **Browser developer tools:** inspect client behavior, storage and network requests.
- **curl:** make minimal, reproducible HTTP requests.
- **OWASP WSTG:** [testing guide](https://owasp.org/www-project-web-security-testing-guide/).

## Workflow

### 1. Establish the test boundary

Record the approved base URLs, hostnames, roles, paths and test window. Configure the proxy to intercept only the authorized application. Disable automatic crawling, active scanning and redirect following until their behavior and target coverage have been reviewed.

### 2. Map the application

Browse the application using the approved test account. Record relevant pages, API calls, user roles, state transitions and security-sensitive workflows. Avoid collecting other users' data or enumerating production records.

### 3. Review security configuration

Inspect HTTPS enforcement, security headers, cookie attributes, cache behavior, error handling, debug output, exposed administrative interfaces and backup or deployment artifacts. Confirm findings against expected configuration and owner context.

### 4. Review authentication and sessions

Within approved test accounts, review password reset flows, MFA behavior, logout, session expiration, cookie flags, token rotation and account recovery. Do not guess passwords, harvest credentials or lock out real users. Do not test account takeover on accounts that the owner has not provided.

### 5. Review authorization

Use two or more owner-created test identities with synthetic records. Compare access between roles and verify that the application enforces access on the server, not only in the user interface. Do not change identifiers to access real customer records. Stop at the first indication that a request would expose data outside the test tenant.

### 6. Review input and output handling

Inspect validation, encoding, file uploads, redirects, error responses and data flows using benign test values. Use non-destructive, minimal probes. Do not use payloads that execute commands, alter production state, exhaust resources or access local/cloud metadata services unless the owner has approved a separate isolated test plan.

### 7. Review business logic and client-side behavior

Test expected workflow invariants using synthetic accounts and transactions. Review trust boundaries, client-side storage, sensitive browser data, cross-origin behavior and state-changing request protections. Never issue transactions, messages, purchases or account changes against real users.

### 8. Use passive scanning first

OWASP ZAP passive analysis or Burp passive observations can help identify missing headers, insecure cookies and suspicious responses. Review each alert manually. Use active scanner features only when explicitly approved and configured for the narrow target and rate limits in the rules of engagement.

### 9. Validate and report

For each suspected issue, capture a minimal request/response excerpt with secrets and personal data redacted. Confirm the behavior with the owner-approved test account, record expected and observed behavior, and map to the relevant WSTG test identifier or OWASP Top 10:2025 category.

## Output

A route and role inventory, reviewed observations, manually validated findings, test limitations and remediation guidance. Use [the finding report template](../../templates/finding-report.md).

## Stop conditions

Stop on unexpected data access, privilege changes, destructive side effects, service instability, unapproved redirects, scope ambiguity or owner request. Do not attempt persistence, stealth, credential theft, brute force, denial-of-service or destructive exploit validation.

## References

- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [OWASP Top 10:2025](https://owasp.org/Top10/2025/)
- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
