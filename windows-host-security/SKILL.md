---
name: windows-host-security
description: Assess authorized Windows workstations and servers for configuration weaknesses, patch exposure, local privilege boundaries, logging, endpoint protection and hardening gaps using read-only checks and approved baselines.
---

# Windows host security assessment

## Purpose

Review the security posture of Windows endpoints and servers that are explicitly included in the engagement. This skill focuses on configuration, patch state, identity boundaries, endpoint controls and audit readiness. It is not an exploitation, credential-dumping or endpoint-evasion workflow.

## Prerequisites

- Written authorization identifies the Windows hosts, operating-system versions, environments and permitted collection methods.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The system owner has approved the account and privilege level used for review; prefer a dedicated least-privilege audit account.
- Confirm whether local policy, registry, event-log and security-product configuration can be read.
- Record the maintenance window and operational contact. Do not install agents or change Group Policy during an assessment unless remediation is separately approved.

## Tooling

- **Microsoft Security Compliance Toolkit (SCT):** compare GPO and local policy with Microsoft security baselines. [Guide](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10)
- **Policy Analyzer / LGPO:** inspect policy differences and export policy snapshots; do not apply settings during assessment. [SCT downloads](https://www.microsoft.com/download/details.aspx?id=55319)
- **Microsoft Defender:** review health and protection status through supported administrative interfaces. [Documentation](https://learn.microsoft.com/microsoft-365/security/defender-endpoint/)
- **Sysmon:** review existing event configuration and logs; do not deploy or alter it without approval. [Documentation](https://learn.microsoft.com/sysinternals/downloads/sysmon)
- **CIS Benchmarks:** use the Windows client/server benchmark matching the exact product release. [Catalog](https://www.cisecurity.org/cis-benchmarks)

## Workflow

### 1. Identify the host and build

Record hostname, domain membership, edition, build, architecture, installed roles, patch level, reboot state and management platform. Confirm the asset against the approved inventory; do not scan adjacent machines.

### 2. Review update and vulnerability posture

Compare installed cumulative/security updates with the vendor's current support and update information. Identify end-of-support builds, missing security updates and vulnerable optional components. Do not infer exploitability from a version string alone; account for backported fixes and installed features.

### 3. Compare security baselines

Use SCT Policy Analyzer or an approved CIS assessment method to compare effective local/GPO settings with the selected baseline. Review:
- Account and lockout policy.
- User Rights Assignment and local administrator membership.
- UAC and remote credential protections.
- Windows Firewall profiles and inbound rules.
- Microsoft Defender status, tamper protection and exclusions.
- BitLocker and recovery-key escrow configuration.
- Secure Boot, VBS/HVCI and credential isolation where supported.
- PowerShell logging, script controls and application control (AppLocker/WDAC).
- Audit policy, Windows Event Forwarding and log retention.

Document intentional deviations and role-specific exceptions rather than treating every benchmark difference as a finding.

### 4. Review remote access and exposed services

Inspect RDP, WinRM, SMB, Remote Registry, RPC exposure, file shares and management agents. Check NLA, encryption, firewall source restrictions, SMB signing/encryption and legacy protocol status. Do not attempt password guessing, remote execution or session collection.

### 5. Review local privilege boundaries

Inventory local administrators, privileged groups, service accounts, scheduled tasks, services, writable service paths, installed drivers and application control policy. Use read-only inspection. Do not extract password hashes, LSASS memory, browser secrets, tokens or private keys.

### 6. Review telemetry and response readiness

Check Defender health, event-log policy, Sysmon configuration if present, central forwarding, time synchronization, alert routing and retention. Avoid clearing logs or generating suspicious activity to test detections.

### 7. Validate and report

For each issue, record host/build, effective setting, expected baseline, source of truth, exception owner, impact and remediation. Include the baseline version and tool version. Redact usernames, machine identifiers and internal paths when not required.

## Output

A host inventory, baseline deviation register, patch/support status, exposed management-service review, endpoint-protection and logging observations, approved exceptions, limitations and prioritized remediation plan.

## Safety constraints

- Read-only collection by default. No remote shell, credential extraction, persistence, evasion or endpoint-control bypass.
- Do not disable antivirus, EDR, firewall or application control.
- Do not change GPO, registry, services, scheduled tasks or user membership during assessment.
- Stop if collection affects availability, accesses unrelated user data or leaves the approved host boundary.

## References

- [Microsoft Security Compliance Toolkit](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/security-compliance-toolkit-10)
- [Windows security baselines](https://learn.microsoft.com/windows/security/operating-system-security/device-management/windows-security-configuration-framework/)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [Microsoft Defender for Endpoint](https://learn.microsoft.com/microsoft-365/security/defender-endpoint/)
