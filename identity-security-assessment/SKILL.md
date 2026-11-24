---
name: identity-security-assessment
description: Assess authorized enterprise identity providers and federation systems for authentication, MFA, lifecycle, privileged access, OAuth/OIDC, SAML, SCIM, service identity and session-management configuration weaknesses.
---

# Identity and federation security assessment

## Purpose

Assess identity providers and identity lifecycle controls used by workforce, customer, service and workload identities. Cover Microsoft Entra ID, Okta, Keycloak, Auth0 and other platforms explicitly listed by the identity owner.

This is a configuration and control review. It does not attempt account takeover, token theft, MFA fatigue or authentication bypass.

## Prerequisites

- Written authorization identifies the identity tenants, applications, domains, test identities, federation partners and permitted methods.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use a dedicated read-only audit role and owner-created synthetic accounts for interactive checks.
- Confirm whether SaaS/third-party identity tenants and external federation partners are in scope.
- Agree on change-freezing and lockout protection. Never test real employee/customer accounts without explicit approval.

## Tools and references

- **Microsoft Entra ID:** identity governance, Conditional Access, audit logs and workload identity controls. [Documentation](https://learn.microsoft.com/entra/identity/)
- **Okta:** sign-on policies, authenticators, lifecycle and system logs. [Documentation](https://developer.okta.com/docs/)
- **Keycloak:** realms, clients, identity brokering and authentication flows. [Documentation](https://www.keycloak.org/documentation)
- **OIDC:** use the current OpenID Connect Core specification. [Specification](https://openid.net/specs/openid-connect-core-1_0.html)
- **OAuth 2.0:** follow current OAuth security best practices. [RFC 9700](https://www.rfc-editor.org/rfc/rfc9700)
- **SAML:** review metadata, signing, encryption, bindings and assertion-consumer configuration against the approved profile. [OASIS SAML](https://www.oasis-open.org/standard/saml/)

## Workflow

### 1. Inventory identity systems and trust

Map IdPs, tenants, domains, realms, identity sources, federation partners, SaaS applications, service principals, workload identities and authentication flows. Record which party owns each trust relationship and the data exchanged.

### 2. Review authentication assurance

Assess MFA coverage, phishing-resistant authentication, authenticator enrollment/recovery, passwordless configuration, conditional access, risk-based policies, legacy authentication, account lockout, session duration and step-up authentication. Use synthetic test identities; do not trigger lockouts or fatigue prompts.

### 3. Review privileged access

Inspect global/tenant administrators, delegated admins, application owners, role-assignable groups, emergency accounts, PIM/JIT controls, approval, activation duration, access reviews and privileged audit logging. Check separation between identity administration and application administration.

### 4. Review OAuth and OIDC applications

Review app registrations, redirect URI restrictions, client type, PKCE, response types, token audience/issuer, scopes, consent policy, client secrets/certificate expiry, signing-key rotation and service-principal permissions. Use provider-issued test clients; do not obtain or replay another user's tokens.

### 5. Review SAML and federation

Review metadata freshness, signing/encryption requirements, certificate rollover, issuer and audience validation, ACS endpoints, NameID, clock skew, assertion lifetime and partner trust. Verify that assertions are validated by the service provider according to its documented profile. Do not forge or replay assertions.

### 6. Review lifecycle and provisioning

Assess joiner/mover/leaver workflows, SCIM provisioning/deprovisioning, guest/external users, inactive identities, orphaned app assignments, group ownership, service accounts, workload identities and access review cadence.

### 7. Review sessions and recovery

Inspect session revocation, refresh-token policy, device/session controls, browser logout, account recovery, help-desk verification and emergency access. Do not collect cookies, refresh tokens, authenticator secrets or recovery codes.

### 8. Review monitoring and governance

Verify sign-in/audit logs, risky sign-in alerts, consent events, admin-role changes, identity lifecycle logs, retention and integration with the security operations team.

### 9. Validate and report

Use approved accounts to confirm expected policy outcomes with minimal, reproducible checks. Record IdP configuration, application identifier (redacted where needed), role, test identity class, policy version, expected/observed state and exceptions.

## Output

An identity architecture and trust map, MFA/authentication posture, privileged-role review, OAuth/OIDC/SAML configuration findings, lifecycle/provisioning gaps, session/recovery observations and remediation plan.

## Safety constraints

- Never steal, dump, forge or replay credentials, tokens, cookies, SAML assertions, API keys or recovery factors.
- Do not perform password spraying, credential stuffing, MFA fatigue, phishing or lockout testing.
- Do not consent to applications, add identities, change roles or disable policies during assessment.
- Stop if an identity boundary or third-party tenant is not explicitly in scope.

## References

- [Microsoft Entra ID documentation](https://learn.microsoft.com/entra/identity/)
- [Okta developer documentation](https://developer.okta.com/docs/)
- [Keycloak documentation](https://www.keycloak.org/documentation)
- [OAuth 2.0 Security Best Current Practice (RFC 9700)](https://www.rfc-editor.org/rfc/rfc9700)
- [OpenID Connect Core](https://openid.net/specs/openid-connect-core-1_0.html)
