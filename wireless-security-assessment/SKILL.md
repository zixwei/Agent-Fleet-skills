---
name: wireless-security-assessment
description: Assess explicitly authorized Wi-Fi, Bluetooth and wireless network deployments for configuration, authentication, encryption, segmentation, device and monitoring weaknesses using passive review and isolated lab validation.
---

# Wireless security assessment

## Purpose

Review enterprise and device wireless deployments, including IEEE 802.11 Wi-Fi, Bluetooth Classic and Bluetooth Low Energy (BLE). Focus on access-point configuration, authentication, encryption, segmentation, device pairing, management and wireless monitoring.

## Prerequisites

- Written authorization identifies SSIDs/BSSIDs, access points, controllers, channels/bands, test devices, BLE devices, physical locations, test window and permitted radio activity.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- The network owner confirms which radios, test APs, clients and BLE peripherals are owned and may be tested.
- Start with passive observation and configuration exports. Active association, frame injection, pairing tests or deauthentication-like behavior require a separate, explicit plan in an isolated test environment.
- Confirm legal radio-frequency and transmission requirements for the location. Do not assume permission to transmit follows from permission to observe.

## Tools

- **Kismet:** passive wireless discovery and monitoring. [Documentation](https://www.kismetwireless.net/docs/)
- **Wireshark / TShark:** analyze approved packet captures. [Documentation](https://www.wireshark.org/docs/)
- **Aircrack-ng:** wireless protocol/security analysis for a dedicated lab AP and test clients. [Documentation](https://www.aircrack-ng.org/documentation.html)
- **hostapd / wpa_supplicant:** build reproducible test AP/client fixtures. [hostapd](https://w1.fi/hostapd/) · [wpa_supplicant](https://w1.fi/wpa_supplicant/)
- **BlueZ / btmon:** inspect Linux Bluetooth host and HCI event behavior. [BlueZ](https://www.bluez.org/)
- **Bluetooth SIG specifications:** validate security requirements and feature behavior against the relevant Core Specification version. [Specifications](https://www.bluetooth.com/specifications/specs/)

## Workflow

### 1. Establish the wireless inventory

Record AP/controller model and firmware, SSIDs, BSSIDs, bands/channels, security mode, management VLAN, guest/IoT networks, BLE gateways and known peripherals. Confirm each identifier against the approved owner inventory.

### 2. Review Wi-Fi configuration

Inspect WPA2/WPA3 settings, protected management frames, transition mode, enterprise authentication, EAP methods, certificate validation, RADIUS configuration, guest isolation, client isolation, management ACLs, WPS status, SSID privacy decisions and key rotation. Document legacy compatibility exceptions.

### 3. Review wireless identity and onboarding

Assess certificate provisioning, device enrollment, onboarding portals, rogue AP detection, certificate trust, shared PSKs, guest expiration, device inventory, BYOD policy and deprovisioning. Do not collect real password material or impersonate users.

### 4. Review BLE and Bluetooth pairing

Inventory discoverability, pairing mode, bonding policy, authentication requirements, encryption, key storage, service/characteristic exposure, device identity, firmware updates and debug interfaces. Use owned peripherals and synthetic characteristics. Avoid pairing with unknown nearby devices.

### 5. Review segmentation and monitoring

Verify that guest, corporate, IoT, management and OT wireless segments map to intended VLANs/ACLs. Review WIDS/WIPS coverage, controller logs, rogue-device alerting, certificate errors and wireless telemetry using owner-provided data.

### 6. Run controlled lab validation

Where active validation is approved, use a test AP, test client and isolated RF environment. Pin the capture, tool and firmware versions; define channel, power, duration, request/frame limits and stop conditions. Validate expected secure behavior using synthetic accounts and packets. Do not disrupt production clients.

### 7. Validate and report

Record AP/BLE identifiers using approved redaction rules, tool/firmware versions, channel, observation time, test setup, configuration evidence, control reference, impact and remediation. Separate passive observations from active tests.

## Output

Wireless asset inventory, Wi-Fi authentication/encryption review, guest/IoT segmentation, BLE pairing/device posture, wireless monitoring coverage, lab-only validation results and prioritized remediation.

## Safety constraints

- Passive monitoring by default. Do not capture payloads containing private user traffic unless explicitly approved and minimized.
- No unauthorized deauthentication, jamming, flooding, impersonation, password guessing or access to third-party wireless devices.
- Do not transmit on production channels or alter AP configuration during assessment.
- Stop if an unknown device, out-of-scope SSID or service impact is observed.

## References

- [NIST SP 800-153 — Guidelines for Securing Wireless Local Area Networks](https://csrc.nist.gov/pubs/sp/800/153/final)
- [Kismet documentation](https://www.kismetwireless.net/docs/)
- [Wireshark documentation](https://www.wireshark.org/docs/)
- [Aircrack-ng documentation](https://www.aircrack-ng.org/documentation.html)
- [Bluetooth SIG specifications](https://www.bluetooth.com/specifications/specs/)
