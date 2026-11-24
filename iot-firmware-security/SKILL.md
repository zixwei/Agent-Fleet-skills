---
name: iot-firmware-security
description: Assess owned IoT and embedded devices for firmware provenance, boot/update integrity, interfaces, debug exposure, cryptography, component vulnerabilities and ecosystem security using offline analysis and bench hardware.
---

# IoT and firmware security assessment

## Purpose

Review the security of an IoT product ecosystem: device hardware, boot chain, firmware, companion applications, cloud services, provisioning, update infrastructure and device communication. Cover Linux-based firmware, RTOS devices, gateways, sensors and embedded controllers.

## Prerequisites

- Written authorization identifies exact devices, board revisions, serial ranges, firmware versions/hashes, companion app, cloud tenant, radio protocols and permitted hardware/firmware operations.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain a spare/owned lab device and vendor-approved firmware image. Do not disassemble, flash, reset or connect production equipment without separate approval.
- Record evidence of ownership and establish a safe power supply, current limit and hardware recovery procedure.
- Keep test devices isolated from production IoT networks and personal home devices.

## Tools

- **EMBA:** firmware analysis, SBOM and vulnerability reporting. [Project](https://github.com/e-m-b-a/emba)
- **Binwalk:** identify and extract firmware structures for offline analysis. [Project](https://github.com/ReFirmLabs/binwalk)
- **FACT:** Firmware Analysis and Comparison Tool. [Project](https://github.com/fkie-cad/FACT_core)
- **Ghidra:** static analysis of embedded binaries. [Project](https://github.com/NationalSecurityAgency/ghidra)
- **QEMU:** emulate supported architectures and local firmware components. [Documentation](https://www.qemu.org/docs/master/)
- **OpenOCD:** inspect supported debug interfaces on owned bench hardware. [Documentation](https://openocd.org/doc/html/)
- **Wireshark:** inspect approved device protocol captures. [Documentation](https://www.wireshark.org/docs/)

## Workflow

### 1. Establish product and ecosystem inventory

Record device model/board revision, SoC/architecture, boot ROM/bootloader, firmware version/hash, vendor support, companion app, cloud endpoints, device identity, radio interfaces and manufacturing/provisioning process.

### 2. Preserve and identify firmware

Obtain firmware through an owner-provided or vendor-supported download. Record source, version, signature metadata and cryptographic hash. Preserve the original image and work on a duplicate. Confirm the license and redistribution terms before retaining or sharing artifacts.

### 3. Perform offline static analysis

Inspect:
- Filesystem type, OS/kernel/base packages and software inventory.
- Bootloader and secure-boot configuration.
- Update package signature verification and anti-rollback metadata.
- Default/debug accounts, hard-coded secrets and certificates; never disclose secret values.
- Init scripts, exposed services, file permissions and unnecessary components.
- Cryptographic libraries, key generation/storage and entropy dependencies.
- Web/API/IPC endpoints, debug flags and insecure protocols.
- SBOM, CVE matches, abandoned dependencies and vendor patch cadence.

Treat scanner output as leads requiring manual verification against the exact firmware build.

### 4. Review hardware interfaces

Use board documentation and approved test points to inventory UART, JTAG/SWD, SPI, I2C, USB, Ethernet and debug headers. Inspect whether production debug access is disabled or controlled. Use only a spare board and approved low-impact measurement; do not modify fuses, secure boot or nonvolatile configuration without explicit approval.

### 5. Review device communication and provisioning

Assess device identity, pairing, mutual authentication, TLS, certificate provisioning, replay protection, telemetry privacy, command authorization, OTA update channels, factory reset and ownership transfer. Use a test gateway and synthetic device identities.

### 6. Use emulation or a hardware bench

Prefer QEMU or a disposable bench fixture for dynamic analysis. Pin firmware, emulation profile and dependencies. Keep the emulator network isolated and use mocked DNS/cloud endpoints. Do not connect emulated or test devices to vendor production infrastructure.

### 7. Assess recovery and lifecycle

Review recovery mode, signed recovery images, rollback behavior, update failure handling, secure decommissioning, credential rotation, vulnerability disclosure, end-of-support policy and supply-chain transparency.

### 8. Validate and report

Record firmware hash, board revision, tool/database versions, exact evidence path, vulnerability references, affected component, exploitability preconditions and whether validation was static, emulated or bench-based. Distinguish an insecure interface from a demonstrated impact.

## Output

Device/firmware inventory, SBOM, boot/update integrity review, exposed hardware/debug interface notes, component vulnerability register, communication/provisioning findings, lifecycle gaps and remediation plan.

## Safety constraints

- Analyze only owned or explicitly authorized devices and firmware.
- Do not alter secure boot, fuses, calibration, radio power, firmware or production device state without explicit approval.
- Do not extract live user data, cloud credentials or private keys.
- Do not install modified firmware on production equipment or use device access to pivot to a network.
- Stop if the board heats abnormally, enters an unrecoverable state or communicates outside the lab boundary.

## References

- [OWASP IoT Security Verification Standard](https://owasp.org/www-project-iot-security-verification-standard)
- [EMBA firmware analyzer](https://github.com/e-m-b-a/emba)
- [Binwalk](https://github.com/ReFirmLabs/binwalk)
- [FACT](https://github.com/fkie-cad/FACT_core)
- [QEMU documentation](https://www.qemu.org/docs/master/)
- [OpenOCD documentation](https://openocd.org/doc/html/)
