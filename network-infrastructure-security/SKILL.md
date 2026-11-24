---
name: network-infrastructure-security
description: Review authorized routers, switches, firewalls, VPNs, DNS, network management and segmentation for configuration, access-control, encryption, logging and exposure weaknesses using approved read-only methods.
---

# Network infrastructure security assessment

## Purpose

Assess the security configuration and management plane of enterprise network infrastructure. Cover routing, switching, firewalls, VPN gateways, wireless controllers, load balancers, DNS/DHCP services and network visibility systems where the owner has authorized the review.

## Prerequisites

- Written authorization identifies device inventory, management networks, network zones, permitted protocols, approved source addresses and test window.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Device owners provide read-only configuration exports or audit identities.
- Obtain an approved topology and list of third-party-managed links, cloud gateways and shared services.
- Agree on change-freezing and emergency stop process. Avoid scanning fragile control-plane or legacy devices.

## Tools

- **Nmap:** inventory approved services and management ports using a narrow target/port list. [Reference](https://nmap.org/book/man.html)
- **Wireshark / TShark:** analyze approved packet captures. [Documentation](https://www.wireshark.org/docs/)
- **Zeek:** review existing network metadata and logs. [Documentation](https://docs.zeek.org/)
- **Greenbone/OpenVAS:** reviewed vulnerability checks where the device owner explicitly approves scanning. [Documentation](https://greenbone.github.io/docs/)
- **Batfish:** analyze supported network configurations and reachability policy offline. [Documentation](https://batfish.readthedocs.io/)
- Vendor tools for the relevant firewall/router/switch platform; use their read-only audit modes.

## Workflow

### 1. Confirm topology and ownership

Map approved sites, VLANs, VRFs, subnets, routing domains, firewall zones, management networks, internet edges, cloud interconnects, remote-access gateways and third-party circuits. Validate device ownership and exclusions.

### 2. Review management-plane security

Inspect management interfaces, admin roles, MFA, AAA/RADIUS/TACACS+, SSH version and algorithms, HTTPS/TLS, SNMPv3, management ACLs, break-glass access, configuration backups and firmware support. Check that management is not exposed to user or public networks.

### 3. Review segmentation and routing

Assess VLAN/VRF boundaries, inter-zone policies, route filters, default routes, peering, transit gateways, guest/IoT isolation, east-west controls, IPv6 policy, network ACLs and cloud/branch connections. Compare intended diagrams with actual read-only configuration. Do not test unauthorized cross-zone access.

### 4. Review firewall and VPN policy

Inventory rule owners, source/destination specificity, service objects, shadowed rules, overly broad egress, temporary exceptions, logging, NAT behavior, remote-access split tunneling, device posture and MFA. Review VPN cryptography and tunnel ownership. Do not modify rules or initiate tunnels.

### 5. Review network services and protocols

Review DNS recursion/zone transfer policy, DHCP trust boundaries, NTP, SNMP, SMB/NFS, directory services, SSH, RDP, remote management, routing protocols and legacy protocols. Use packet captures supplied by the owner or approved passive observation points.

### 6. Review resilience and observability

Inspect centralized syslog, flow telemetry, NDR/IDS sensors, configuration change tracking, control-plane protection, high availability, backup/restore, time synchronization and incident escalation. Do not fail over devices or trigger control-plane stress.

### 7. Validate and report

Correlate configuration exports, approved service inventory, packet metadata and policy analysis. Record device model/firmware, interface/zone, rule IDs, evidence source, owner, business purpose, compensating controls and remediation.

## Output

A verified network asset/topology map, management-plane review, segmentation matrix, firewall/VPN findings, service exposure register, observability/resilience gaps and prioritized remediation.

## Safety constraints

- Prefer configuration review and passive telemetry. Do not conduct denial-of-service, packet flooding, disruptive protocol tests or unauthorized wireless actions.
- Do not change routing, firewall, NAT, VPN, ACL, firmware, credentials or device state.
- Do not capture payloads containing user data unless specifically approved and minimized.
- Stop if a device is unstable, a shared-provider boundary is unclear or monitoring indicates service impact.

## References

- [NIST SP 800-115](https://csrc.nist.gov/pubs/sp/800/115/final)
- [Nmap Reference Guide](https://nmap.org/book/man.html)
- [Wireshark Documentation](https://www.wireshark.org/docs/)
- [Zeek Documentation](https://docs.zeek.org/)
- [Batfish Documentation](https://batfish.readthedocs.io/)
