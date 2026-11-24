---
name: active-directory-security
description: Review an explicitly authorized Active Directory environment for directory configuration, delegation, Kerberos and LDAP protections, trusts, Group Policy, privileged groups and AD CS exposure using bounded read-only assessment methods.
---

# Active Directory security assessment

## Purpose

Assess on-premises Active Directory Domain Services (AD DS), domain controllers and related identity infrastructure. Focus on configuration and privilege boundaries, not compromising accounts or moving through the domain.

## Prerequisites

- Written authorization names the forest/domains, domain controllers, test window, permitted collection methods and approved audit identities.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Coordinate with the AD owner and SOC. Directory collection can be sensitive even when read-only.
- Use a dedicated audit account with the minimum read rights needed. Do not use a Domain Admin account solely for convenience.
- Confirm whether AD CS, Entra Connect, trusts, privileged access workstations and third-party identity services are in scope.

## Tools

- **PingCastle:** directory health and configuration assessment. [Official site](https://www.pingcastle.com/)
- **BloodHound Community Edition:** visualize directory relationships and privilege paths from approved collection data. [Documentation](https://bloodhound.specterops.io/)
- **AD Explorer:** inspect directory objects and ACLs. [Sysinternals](https://learn.microsoft.com/sysinternals/downloads/adexplorer)
- **Microsoft Security Compliance Toolkit:** review domain and server policy baselines. [Guide](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10)
- **Certipy:** AD CS configuration auditing; use audit/read-only functionality only. [Project](https://github.com/ly4k/Certipy)
- **Microsoft Group Policy tooling:** inspect resultant policy and GPO ownership using approved administrative interfaces.

## Workflow

### 1. Map the approved directory boundary

Record forest/domain names, functional levels, domain controllers, sites, trusts, DNS zones, AD CS servers, Entra Connect and management systems. Verify each system belongs to the authorized environment.

### 2. Review privileged identity design

Inspect membership and nesting of Domain Admins, Enterprise Admins, Schema Admins, built-in administrators, delegated operator groups and privileged service identities. Review tiering, privileged access workstations, just-in-time access, break-glass accounts, stale accounts and service-account ownership.

Do not authenticate as another user, harvest credentials or test passwords.

### 3. Review Kerberos and NTLM configuration

Inspect supported encryption types, legacy RC4/DES use, service account ownership, delegation settings, unconstrained delegation exposure, resource-based constrained delegation design, NTLM restrictions and time synchronization. Record configuration evidence; do not conduct ticket extraction, cracking or password roasting.

### 4. Review LDAP and domain-controller protections

Review LDAP signing and channel binding, LDAP over TLS, SMB signing, secure dynamic DNS, domain-controller firewall policy, remote management exposure, patch level, auditing and backup protection. Verify that DC administrative interfaces are reachable only from approved management networks.

### 5. Review Group Policy and delegation

Inventory GPO links, inheritance, enforced/blocked settings, privileged GPO editors, SYSVOL/NETLOGON permissions, logon scripts and policy drift. Review OU delegation, ACL ownership and permissions that allow unintended modification of users, groups, computers or policies.

### 6. Review trusts and AD CS

Document forest/external trusts, SID filtering and selective authentication settings. For AD CS, inventory CAs, templates, enrollment rights, subject/SAN rules, manager approval, EKUs, private-key protection and CA administration. Use configuration review only; do not request certificates or impersonate identities to prove an escalation path.

### 7. Review monitoring and recovery

Check audit policy, DC event forwarding, identity alerts, replication health, privileged-change monitoring, system-state backups and tested recovery plans. Do not alter replication state or clear logs.

### 8. Validate and report

Correlate findings with vendor guidance and the exact configuration. Record collection identity, tool/version, timestamp, evidence source, business impact, compensating controls and remediation. Keep directory exports private and minimize object attributes.

## Output

A directory architecture map, privileged-group review, Kerberos/LDAP posture, GPO/ACL deviations, trust and AD CS assessment, monitoring/recovery observations, limitations and prioritized remediation recommendations.

## Safety constraints

- Do not perform DCSync, credential dumping, password spraying, Kerberoasting, AS-REP roasting, ticket forging, relay, lateral movement or remote command execution.
- Do not create or alter users, groups, GPOs, trusts, certificates or directory ACLs.
- Do not export password hashes, secrets, tokens or unrelated personal attributes.
- Stop if collection exceeds approved permissions or generates unexpected authentication load.

## References

- [Microsoft AD security guidance](https://learn.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory)
- [Microsoft Security Compliance Toolkit](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10)
- [BloodHound CE documentation](https://bloodhound.specterops.io/)
- [PingCastle](https://www.pingcastle.com/)
- [Certipy](https://github.com/ly4k/Certipy)
