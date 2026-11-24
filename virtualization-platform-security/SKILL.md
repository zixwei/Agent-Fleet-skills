---
name: virtualization-platform-security
description: Review authorized VMware vSphere/ESXi, Hyper-V, Proxmox VE and KVM/libvirt platforms for management-plane, host hardening, VM isolation, access, storage, backup and lifecycle configuration weaknesses.
---

# Virtualization platform security assessment

## Purpose

Assess hypervisors, management servers, virtual networking, VM lifecycle, storage and administrative boundaries. This skill covers data-center and private-cloud virtualization platforms.

## Prerequisites

- Written authorization identifies vCenter/ESXi hosts, Hyper-V clusters, Proxmox nodes, KVM/libvirt hosts, management systems, datastores and permitted read-only API access.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain read-only audit roles and an approved host inventory. Confirm whether virtual networking, backup systems and management appliances are in scope.
- Do not perform snapshot, migration, power-state or configuration changes during assessment.

## Platform coverage

- **VMware vSphere / ESXi:** host configuration, vCenter roles, services, lockdown mode, management network, vSwitch/vDS, VM encryption, datastore and lifecycle. [CIS ESXi benchmarks](https://www.cisecurity.org/benchmark/vmware-esxi)
- **Microsoft Hyper-V:** host and cluster management, constrained delegation, virtual switches, VM generation/security, shielded VM and admin boundaries. [Microsoft Hyper-V](https://learn.microsoft.com/windows-server/virtualization/hyper-v/)
- **Proxmox VE:** node access, API tokens/roles, cluster network, firewall, storage and backup. [Documentation](https://pve.proxmox.com/pve-docs/)
- **KVM/libvirt:** host access, libvirt sockets, virtual networks, storage pools, QEMU process isolation, SELinux/AppArmor and VM device access. [libvirt documentation](https://libvirt.org/docs.html)

## Tools

Use vendor-supported read-only APIs and configuration exports. Examples include VMware PowerCLI read-only cmdlets, govc inventory commands, Proxmox read-only API endpoints, Hyper-V PowerShell Get-* cmdlets and libvirt `virsh` inventory commands. Validate command behavior before running it; avoid state-changing subcommands.

Use CIS Benchmarks for the exact platform and version. Record the benchmark version rather than mixing ESXi, vCenter and workload recommendations.

## Workflow

### 1. Inventory platform and control plane

Record product/version, host/cluster membership, management endpoints, hardware, support status, license features, management plane dependencies and administrative network paths.

### 2. Review administrator access

Inspect role assignments, MFA/SSO, privileged groups, API tokens, service accounts, emergency access, session timeout and audit logging. Check for shared administrator identities and orphaned service credentials. Never retrieve secret material.

### 3. Review host hardening

Assess patch support, management services, SSH/shell enablement, lockdown mode, Secure Boot, TPM/attestation, host firewall, time synchronization, local accounts and audit forwarding. Document vendor-managed settings and maintenance constraints.

### 4. Review VM isolation and virtual networking

Inspect virtual switches, port groups, VLAN tagging, promiscuous/forged-transmit/MAC-change policies, host-only networks, VM-to-host device access, shared folders, clipboard/USB redirection, nested virtualization and inter-tenant boundaries.

### 5. Review VM and storage protection

Assess VM encryption, virtual TPM, secure boot, snapshots, templates, disk formats, datastore permissions, storage encryption, replication, backup immutability and recovery testing. Do not mount or copy guest disks containing customer data.

### 6. Review lifecycle and logging

Inspect image/template provenance, golden-image maintenance, patch orchestration, VM retirement, orphaned snapshots, dormant VMs, configuration backups, event logs and change tracking.

### 7. Validate and report

Map findings to the exact platform/version benchmark. Include host/cluster identifier, effective setting, expected baseline, operational impact, exception and remediation owner.

## Output

A platform inventory, administrator-role review, host hardening report, virtual network/VM isolation review, storage/encryption findings, backup/lifecycle gaps and prioritized remediation.

## Safety constraints

- Read-only collection only. No VM power operations, snapshots, migrations, hypervisor shell execution or firmware modifications.
- Do not attempt VM escape, host takeover, cross-tenant access or access to guest contents.
- Do not expose datastore paths, VM names or tenant identifiers beyond the approved report.
- Stop if a command's behavior is unclear or an operation would alter cluster state.

## References

- [CIS Benchmarks — VMware ESXi](https://www.cisecurity.org/benchmark/vmware-esxi)
- [Microsoft Hyper-V documentation](https://learn.microsoft.com/windows-server/virtualization/hyper-v/)
- [Proxmox VE documentation](https://pve.proxmox.com/pve-docs/)
- [libvirt documentation](https://libvirt.org/docs.html)
