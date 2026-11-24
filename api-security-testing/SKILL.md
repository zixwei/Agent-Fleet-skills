---
name: api-security-testing
description: Assess authorized APIs for authentication, object and function authorization, data exposure, input validation, and configuration issues using synthetic records and bounded requests.
---

# API security testing

## Purpose

Use this skill to assess APIs that are explicitly included in an engagement. The workflow focuses on the OWASP API Security Top 10 and on the application's intended authorization model.

## Prerequisites

- Written authorization explicitly covers the API base URL, environment, methods and test types.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- API documentation or an OpenAPI schema is supplied by the owner where possible.
- The owner has provided dedicated test accounts, roles, tenants and synthetic records.
- Rate limits, request quotas, state-changing permissions and emergency contacts are known.

## Tools

- **Burp Suite:** intercept and compare requests — [documentation](https://portswigger.net/burp/documentation).
- **OWASP ZAP:** API and OpenAPI support — [documentation](https://www.zaproxy.org/docs/).
- **Postman:** organize approved API collections — [documentation](https://learning.postman.com/).
- **curl:** minimal reproducible requests — [manual](https://curl.se/docs/).
- **OWASP API Security Top 10:** [official project](https://owasp.org/projects/api-security-project).

## Workflow

### 1. Establish an API inventory

Record the approved base URL, API version, methods, authentication model, roles, object types, tenant boundaries and documented quotas. Keep test and production environments separate. Do not import undocumented endpoints discovered on unrelated hosts into scope.

### 2. Understand authentication

Review token issuance, expiry, audience, issuer, signature validation and revocation using owner-provided test identities. Check for sensitive information in tokens and logs without publishing token contents. Do not forge or steal credentials, crack secrets or use tokens belonging to other people.

### 3. Test object-level authorization safely

Use two owner-created test users and synthetic objects. With permission, create a test object under User A and verify the expected access behavior for User B. Confirm the server enforces ownership and tenant boundaries. Do not substitute identifiers from real users or enumerate production object IDs.

### 4. Test function-level authorization

Compare permitted actions across documented roles using test accounts. Verify that administrative or privileged operations are denied to lower-privilege roles. Do not invoke destructive endpoints or actions that change real customer data.

### 5. Review property-level authorization and data exposure

Compare responses for the same synthetic object across approved roles. Check whether sensitive fields are returned or accepted unexpectedly. Avoid requesting or recording personal, financial, authentication or other sensitive production fields.

### 6. Review input and resource handling

Review schema validation, content types, parameter binding, pagination, file handling and error responses. Use bounded benign values and small request bodies. Test rate limiting only within owner-approved quotas; do not exhaust shared resources or bypass service controls.

### 7. Review API configuration

Inspect CORS, TLS, caching, error messages, versioning, deprecated endpoints and documentation exposure. Treat server banners and schema details as context, not standalone vulnerabilities.

### 8. Validate findings

Repeat the minimal check with a second approved test identity where relevant. Record the exact endpoint, role, synthetic object, expected response, observed response and correlation/request ID. Redact tokens and sensitive headers from all evidence.

## Output

An API inventory and findings mapped, where appropriate, to OWASP API Security Top 10 categories such as broken object-level authorization, broken authentication, broken function-level authorization, unrestricted resource consumption and security misconfiguration.

## Safety constraints

- Do not access another user's data or guess production identifiers.
- Do not run uncontrolled fuzzing, brute force or high-volume parameter enumeration.
- Do not replay payment, messaging, deletion or transaction requests against real data.
- Do not use third-party APIs unless they are explicitly in scope.
- Stop if a response includes data that is not part of the approved test fixture.

## References

- [OWASP API Security Top 10](https://owasp.org/projects/api-security-project)
- [OWASP REST Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/REST_Security_Cheat_Sheet.html)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
