---
name: linux-host-security
description: Assess authorized Linux servers and workstations for patch, identity, privilege, service, SSH, kernel, filesystem, audit and mandatory-access-control weaknesses using read-only configuration reviews.
---

# Linux host security assessment

## Purpose

Review Linux hosts and distributions included in an engagement. Cover operating-system hardening, identity and privilege configuration, services, filesystem permissions, kernel posture, logging and host-level network controls.

## Prerequisites

- Written authorization names the hosts, distribution/version, environment and permitted collection method.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use a dedicated audit account; prefer non-root collection and request narrowly scoped read-only privilege only when required.
- Confirm production change restrictions, package-manager access, maintenance windows and emergency contact.
- Do not run exploit-suggester scripts or untrusted shell scripts on the host.

## Tools

- **Lynis:** local security audit and hardening suggestions. [Documentation](https://cisofy.com/lynis/)
- **OpenSCAP:** configuration compliance assessment and SCAP content. [Project](https://www.open-scap.org/)
- **osquery:** read-only system inventory with SQL-like queries. [Documentation](https://osquery.io/)
- **systemd-analyze security:** review service sandboxing properties. [Manual](https://www.freedesktop.org/software/systemd/man/latest/systemd-analyze.html)
- **auditd:** review existing audit rules and event coverage. [Linux Audit](https://github.com/linux-audit/audit-userspace)
- **CIS Benchmarks:** choose the exact distribution/version benchmark. [Catalog](https://www.cisecurity.org/cis-benchmarks)

## Workflow

### 1. Identify host and support status

Record distribution, release, kernel, architecture, role, package sources, uptime, virtualization/container status and support lifecycle. Verify it against scope and the asset inventory.

### 2. Review patch and package posture

Inspect installed security updates, repositories, signing configuration, unsupported packages and vulnerable dependencies. Distinguish installed package versions from upstream release numbers and distribution backports. Do not run automatic upgrades during assessment.

### 3. Review users, groups and privilege paths

Inspect local users, interactive accounts, UID 0 entries, group membership, sudo policy, PAM stack, password-aging policy, service accounts and inactive accounts. Check sudoers ownership and include files. Never read password hashes or test credentials.

### 4. Review SSH and remote administration

Check SSH protocol and cryptographic configuration, root login policy, authentication methods, allowed users/groups, forwarding features, host keys, management network restrictions and logging. Review console, serial, management-agent and remote-control access where applicable.

### 5. Review services and scheduled execution

Inventory listening services, enabled systemd units, socket activation, timers, cron jobs, package managers, container daemons and management agents. Review service users, writable paths, capabilities, environment files and hardening directives. Do not stop, restart or alter services.

### 6. Review filesystem and kernel protections

Inspect sensitive file/directory ownership, permission boundaries, mount flags, world-writable paths, Linux capabilities, setuid/setgid inventory, kernel modules, sysctl values, namespaces and container isolation. Review SELinux/AppArmor state and policy mode.

### 7. Review host firewall, audit and logging

Inspect nftables/iptables/firewalld rules, IPv6 policy, audit rules, journald/rsyslog retention, time synchronization, log forwarding, EDR/monitoring agents and backup protection. Do not clear logs or modify firewall policy.

### 8. Validate and report

Compare results against the applicable CIS/OpenSCAP profile and vendor guidance. Include distro version, kernel, exact setting, baseline reference, tool version, exception and compensating control. Redact usernames, internal paths and host metadata when not needed.

## Output

A host inventory, patch/support status, identity and sudo review, SSH posture, service inventory, filesystem/kernel findings, mandatory-access-control posture, logging/firewall observations and prioritized remediation plan.

## Safety constraints

- Read-only by default. Do not run exploit code, privilege-escalation payloads, rootkits or persistence tests.
- Do not change permissions, users, sudoers, SSH, firewall, kernel parameters or services during assessment.
- Do not expose secrets in shell output, command history, process lists or reports.
- Stop if the assessment would disrupt an active service or exceeds the authorized host boundary.

## References

- [Lynis](https://cisofy.com/lynis/)
- [OpenSCAP](https://www.open-scap.org/)
- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [Linux Audit Project](https://github.com/linux-audit/audit-userspace)
