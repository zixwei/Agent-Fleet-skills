---
name: ics-ot-security-assessment
description: Assess explicitly authorized industrial control and operational technology environments for architecture, segmentation, remote access, asset, configuration, safety, resilience and monitoring weaknesses using passive and offline methods.
---

# ICS and OT security assessment

## Purpose

Review industrial control systems and operational technology (OT), including SCADA, DCS, PLCs, RTUs, HMIs, engineering workstations, historians, building automation, transportation and industrial IoT. Safety, process availability and deterministic operation take priority over conventional IT testing.

## Prerequisites

- Written authorization identifies site, process, OT zones/conduits, asset owners, devices, protocols, maintenance window, allowed passive/active methods, emergency shutdown authority and safety contact.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain approved OT architecture, asset register, process hazard/safety constraints and change-control requirements.
- Coordinate with operations, safety engineers, control-system vendors and the incident response team.
- Default to passive observation and configuration review. No active probing of PLCs, controllers or safety systems without a device-specific test plan approved by operations and safety leadership.

## Standards and tools

- **NIST SP 800-82 Rev. 3:** current final OT security guide. Rev. 4 was published as an initial public draft on September 21, 2026; record it as draft until finalized. [Rev. 3](https://csrc.nist.gov/pubs/sp/800/82/r3/final) · [Rev. 4 draft](https://csrc.nist.gov/pubs/sp/800/82/r4/ipd)
- **MITRE ATT&CK for ICS:** map observed behaviors and defensive coverage. [Matrix](https://attack.mitre.org/matrices/ics/)
- **IEC 62443:** reference the applicable industrial automation and control system security requirements; use licensed standards through the owner.
- **CISA ICS advisories and guidance:** prioritize vendor-specific advisories and mitigations. [ICS resources](https://www.cisa.gov/topics/industrial-control-systems)
- **Wireshark / Zeek:** analyze owner-provided or approved passive packet/metadata captures. [Wireshark](https://www.wireshark.org/docs/) · [Zeek](https://docs.zeek.org/)
- **GRASSMARLIN:** passive network situational awareness where supported. [Project](https://github.com/iadgov/GRASSMARLIN)
- **OpenPLC / MiniCPS:** build isolated training simulations; never attach simulations to production control networks. [OpenPLC](https://github.com/thiagoralves/OpenPLC_v3) · [MiniCPS](https://github.com/scy-phy/miniCPS)

## Workflow

### 1. Establish the process and safety boundary

Document physical process, safety instrumented systems, operating modes, critical control functions, safe state, dependencies and prohibited actions. Identify which assets are safety-critical and which activities may affect availability or process safety.

### 2. Map OT assets and zones

Inventory PLCs, RTUs, HMIs, engineering stations, historians, gateways, jump hosts, remote access, wireless links, safety controllers and vendor support connections. Map Purdue/zone-conduit architecture and validate ownership. Passive observations are candidate assets until confirmed by the owner.

### 3. Review segmentation and remote access

Inspect IT/OT separation, firewalls, unidirectional gateways, remote vendor access, MFA, jump servers, removable media workflows, protocol allowlists, egress restrictions and monitoring. Prefer configuration exports and diagrams; do not attempt to cross zone boundaries.

### 4. Review industrial protocols and device posture

Review protocol use, legacy cleartext services, authentication, integrity protections, device firmware/support, engineering software, default accounts, service exposure and time synchronization. Use packet metadata and vendor configuration. Do not issue write, start/stop, reset, firmware-download or programming commands to controllers.

### 5. Review safety and change management

Assess engineering workstation hardening, code signing/version control for logic, backup/restore, change approval, safety interlocks, controller program baselines, vendor support, patch windows and recovery testing. Do not modify ladder logic, control logic, setpoints or safety parameters.

### 6. Review visibility and incident response

Inspect passive monitoring, network sensors, asset discovery, centralized logs, alarm handling, time synchronization, removable media logging, remote access records and incident playbooks. Review whether SOC procedures understand process impact and escalation to operations.

### 7. Use an isolated simulation for active testing

When a protocol or device behavior needs active validation, reproduce it in a standalone OpenPLC/MiniCPS or vendor-approved simulator. Use synthetic process values, no physical actuators and no routed connection to operational networks. Record simulator version and scenario limits.

### 8. Prioritize and report

Use safety and mission impact, not only CVSS, to rank findings. Record affected process/zone, device role, firmware, protocol, vendor ownership, evidence method, confidence, operational consequences, compensating controls and owner-approved remediation. Map relevant techniques to ATT&CK for ICS without inferring activity from a control gap.

## Output

OT process and asset map, zone/conduit review, remote access and segmentation findings, device/protocol posture, safety/change-control observations, monitoring/recovery gaps and risk-prioritized recommendations.

## Safety constraints

- Do not run vulnerability scans, fuzzers, exploit modules or active discovery against live PLCs/controllers unless a separate device- and process-specific plan is approved by operations and safety leadership.
- Never send control commands, alter process values, modify logic, stop equipment or test safety interlocks on a live process.
- No denial-of-service, packet flooding, protocol writes, firmware changes or uncontrolled broadcasts.
- Stop immediately if process alarms, device instability, unexpected controller traffic or operational impact occurs.

## References

- [NIST SP 800-82 Rev. 3 final](https://csrc.nist.gov/pubs/sp/800/82/r3/final)
- [NIST SP 800-82 Rev. 4 initial public draft](https://csrc.nist.gov/pubs/sp/800/82/r4/ipd)
- [MITRE ATT&CK for ICS](https://attack.mitre.org/matrices/ics/)
- [CISA Industrial Control Systems](https://www.cisa.gov/topics/industrial-control-systems)
- [GRASSMARLIN](https://github.com/iadgov/GRASSMARLIN)
