---
name: android-application-security
description: Assess authorized Android applications and devices for insecure storage, platform integration, network, component, authentication, privacy and binary configuration weaknesses using OWASP mobile testing guidance.
---

# Android application security assessment

## Purpose

Review Android applications (APK/AAB-derived builds), supporting APIs and app-to-platform integrations. Cover native Java/Kotlin apps and identify cross-platform frameworks where present. Determine whether application security controls are implemented correctly using test accounts and synthetic data.

## Prerequisites

- Written authorization identifies application ID, exact build/version/hash, Android versions/devices, backend endpoints, test accounts, test window and permitted dynamic techniques.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain a test build and a dedicated test device/emulator; use a clean snapshot and synthetic accounts/data.
- Define whether source code, backend APIs, third-party SDKs and anti-tamper controls are in scope.
- Do not test consumer devices, production accounts or third-party apps without explicit separate authorization.

## Standards and tools

Use stable OWASP MASVS, MASTG v2.0.0 and MASWE v1.0.0 references. Map findings to exact control/test identifiers and record the version used.

- **MobSF:** static and dynamic mobile application analysis. [Project](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- **JADX:** decompile DEX artifacts for source-level review. [Project](https://github.com/skylot/jadx)
- **Apktool:** inspect APK resources and manifest. [Project](https://apktool.org/)
- **Frida:** dynamic instrumentation in an owned, isolated test process/device. [Documentation](https://frida.re/docs/)
- **Objection:** mobile runtime inspection with a bounded test plan. [Project](https://github.com/sensepost/objection)
- **Burp Suite / OWASP ZAP:** inspect app API traffic using a test CA only on a dedicated test device. [Burp](https://portswigger.net/burp/documentation) · [ZAP](https://www.zaproxy.org/docs/)

## Workflow

### 1. Identify and preserve the build

Record package name, version, build type, signing certificate fingerprint, target/min SDK, ABI, build provenance and file hash. Distinguish debug/test builds from production releases. Preserve an unmodified source artifact.

### 2. Static analysis

Inspect:
- Manifest exported components, permissions, intent filters, providers and backup settings.
- Network Security Configuration, cleartext traffic policy, certificate trust and debug flags.
- Embedded API endpoints, test credentials, secrets, signing/debug material and sensitive logging.
- WebView JavaScript/content access, file access, navigation allowlists and mixed content.
- Deep links, app links, intent validation, pending intents and inter-app communication.
- Local storage, SQLite/Room, shared preferences, files, clipboard, screenshots and backup behavior.
- Cryptography choices, key derivation and Android Keystore use.
- Third-party SDK inventory, outdated dependencies, tracking and data collection.

Do not publish secret values discovered in the package. Report their locations and request owner-led rotation.

### 3. Dynamic analysis

Use an emulator/device owned by the assessment team. Verify app identity and test account before every session. Observe runtime storage, IPC, network behavior, authentication/session lifecycle, permission prompts and privacy behavior. Use Frida/Objection only against the test application and never to extract live user credentials or bypass controls on unrelated apps.

### 4. API and backend boundary

Map API calls and compare them with the [API security testing](../api-security-testing/SKILL.md) workflow. Use test identities and synthetic objects. Verify authorization, input validation, rate/resource controls and sensitive-data minimization. Do not enumerate real users or retrieve unrelated records.

### 5. Platform and resilience checks

Review Android version compatibility, deprecated APIs, exported components, app links, biometric integration, root/debugger handling, anti-tamper signals, backup/restore and update/signing process. Treat anti-tamper bypass as out of scope unless an isolated, specifically approved test plan exists; never use it to conceal activity.

### 6. Validate and report

Reproduce findings on the exact build and record MASTG test ID, MASVS control, MASWE weakness where applicable, device/OS, app version, test-account class, sanitized evidence and remediation. Validate fixed builds where available.

## Output

Build fingerprint and inventory, static-analysis register, runtime observations, platform/API findings, privacy/data-flow notes, MASVS/MASTG/MASWE mapping, limitations and remediation recommendations.

## Safety constraints

- Use only owned devices/emulators, test builds and synthetic data.
- Do not extract passwords, session tokens, private keys or personal records.
- Do not repack, resign, redistribute or modify a client's app without explicit permission.
- No persistence, malware, unauthorized remote access, destructive payloads or testing against third-party apps.
- Stop if the app reaches a non-test backend, exposes real user data or leaves the approved application boundary.

## References

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)
- [OWASP MASWE](https://mas.owasp.org/MASWE/)
- [MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- [JADX](https://github.com/skylot/jadx)
- [Frida](https://frida.re/docs/)
