---
name: security-lab-management
description: Design, build, operate and retire isolated penetration-testing laboratories with explicit asset manifests, network containment, snapshots, synthetic identities, resource limits, evidence handling and reproducible scenarios.
---

> 边界:仅限授权测试。只用于已获书面授权的渗透测试目标。

# Security lab management

## Purpose

Provide a repeatable lifecycle for safe training, exploit validation, malware-free binary analysis and protocol testing. Labs must be isolated from production/home networks, have clear ownership, and be reproducible from documented manifests.

## Prerequisites

- Define the lab purpose, owner, scenario, assets, allowed methods, expected outputs and teardown date.
- Confirm all images, VMs, vulnerable applications and firmware are legally obtained and permitted for training.
- Use a dedicated host or isolated hypervisor environment. Do not run intentionally vulnerable targets on a production workstation with sensitive data.
- Define resource limits, storage quotas, emergency stop and backup/restore plan.
- Use synthetic accounts and records only.

## Supported platforms and references

- **VirtualBox:** host-only/internal networks and VM snapshots. [Manual](https://www.virtualbox.org/manual/ch06.html)
- **QEMU/KVM:** isolated local VMs and architecture emulation. [Documentation](https://www.qemu.org/docs/master/)
- **Docker / Podman:** disposable containers and internal networks. [Docker network drivers](https://docs.docker.com/engine/network/drivers/) · [Podman docs](https://docs.podman.io/)
- **Vagrant:** reproducible local VM provisioning. [Docs](https://developer.hashicorp.com/vagrant/docs)
- **OWASP Juice Shop / WebGoat / crAPI:** intentionally vulnerable application training. [Juice Shop](https://owasp.org/www-project-juice-shop/) · [WebGoat](https://owasp.org/www-project-webgoat/) · [crAPI](https://owasp.org/www-project-crapi/)
- **Metasploitable 2:** deliberately vulnerable VM for local training. [Guide](https://docs.rapid7.com/metasploit/metasploitable-2/)

## Lab lifecycle

### 1. Define a lab manifest

Record lab ID, purpose, owner, test scope, asset image/version/digest, OS/architecture, networks, exposed services, tool versions, scenario steps, time/resource limits, data classification and expiry date. See the lab-record schema and example.

### 2. Design network isolation

Prefer host-only or internal virtual networks with no default route to production or the public Internet. Separate target, tooling and observer networks where required. Disable bridging, host networking, port publishing, shared folders, clipboard and USB passthrough unless required and reviewed. Where egress is temporarily needed to acquire signed packages, do so before introducing vulnerable targets and remove the route first.

### 3. Provision from trusted artifacts

Use official images/releases, verify signatures and checksums where available, record digests and licenses, and pin versions. Do not execute downloaded install scripts without review. Keep vulnerable images separate from credentials and personal data.

### 4. Establish known-good state

Take a clean snapshot or immutable image checkpoint. Verify boot, network routes, DNS, firewall and service exposure. Record baseline hashes and confirm the target cannot route to unintended networks.

### 5. Run the approved scenario

Use synthetic data, dedicated test identities, bounded concurrency and timeouts. Keep an operator present for stateful tests. Capture only necessary packets/logs; avoid storing credentials and unrelated host data. Recheck network isolation before every scenario.

### 6. Restore and verify teardown

Stop services, restore snapshots, delete temporary secrets and test accounts, clear only lab-generated artifacts according to the retention policy and verify network routes are removed. Record cleanup status and any residual artifacts.

### 7. Maintain reproducibility

Track image digests, tool versions, scenario manifests, test inputs, expected results and observed results in version control where no secrets or proprietary content are included. Rebuild periodically to confirm setup instructions still work.

## Output

A lab manifest, network topology, image/tool inventory, isolation verification, scenario run record, evidence index, snapshot/rollback reference and teardown report.

## Safety constraints

- Never bridge intentionally vulnerable targets to production, public or home networks.
- No real credentials, personal data, customer data or production backups in lab fixtures.
- Do not publish vulnerable services or expose default credentials to the Internet.
- Set CPU, memory, storage, process, network and time limits to prevent accidental host exhaustion.
- Stop if a route, shared folder, port or process crosses the defined lab boundary.

## References

- [VirtualBox Networking](https://www.virtualbox.org/manual/ch06.html)
- [Docker network drivers](https://docs.docker.com/engine/network/drivers/)
- [QEMU documentation](https://www.qemu.org/docs/master/)
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [OWASP WebGoat](https://owasp.org/www-project-webgoat/)
- [Metasploitable 2](https://docs.rapid7.com/metasploit/metasploitable-2/)
