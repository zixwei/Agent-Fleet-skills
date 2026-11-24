---
name: ios-application-security
description: Assess authorized iOS and iPadOS applications for data protection, Keychain, networking, platform integrations, privacy, binary configuration and runtime weaknesses using OWASP mobile testing guidance.
---

# iOS application security assessment

## Purpose

Review native iOS/iPadOS apps, IPA builds and their supporting services. Examine platform security, app sandboxing, data protection, Keychain, transport, URL handling, privacy and release/signing configuration.

## Prerequisites

- Written authorization identifies bundle ID, exact build/hash, iOS versions/devices, test backend, test accounts and permitted dynamic analysis.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use an owned test device or simulator and a dedicated test build/account. Confirm jailbreak/runtime instrumentation scope separately.
- Obtain source, symbol files and provisioning/build metadata where available.
- Do not install a client build on a personal device or use real Apple IDs/customer accounts without explicit approval.

## Standards and tools

Use stable OWASP MASVS, MASTG v2.0.0 and MASWE v1.0.0 identifiers. Record exact versions and applicable platform controls.

- **MobSF:** static and dynamic iOS artifact review. [Project](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- **Ghidra:** analyze Mach-O binary structures and compiled code. [Project](https://github.com/NationalSecurityAgency/ghidra)
- **Frida:** instrument an owned test app/device. [Documentation](https://frida.re/docs/)
- **Objection:** inspect runtime behavior in controlled builds. [Project](https://github.com/sensepost/objection)
- **Burp Suite / mitmproxy:** observe test API traffic using a dedicated test trust configuration. [Burp](https://portswigger.net/burp/documentation) · [mitmproxy](https://docs.mitmproxy.org/)

## Workflow

### 1. Identify the binary and signing context

Record bundle ID, marketing/build version, minimum OS, architectures, code-signing identity, entitlements, provisioning profile, build configuration, symbols and SHA-256. Separate simulator, development, ad hoc and App Store builds.

### 2. Static application review

Inspect:
- Info.plist URL types, query schemes, privacy usage descriptions, background modes and ATS policy.
- Entitlements, app groups, keychain access groups, associated domains and push capabilities.
- Keychain accessibility classes, Secure Enclave/Keychain usage, data-protection file classes and backup exclusions.
- Local databases, preferences, caches, logs, screenshots, pasteboard and document handling.
- URL schemes, Universal Links, custom routing, WebViews and universal-link trust.
- TLS configuration, certificate validation, ATS exceptions and sensitive endpoint strings.
- Embedded secrets, debug symbols, test flags, third-party SDKs and privacy manifests.
- IPC, extensions, app groups, widgets, share extensions and document providers.

Never copy secret values into notes or reports; describe the location and request owner-led rotation.

### 3. Dynamic review

Run the test build with synthetic identities. Observe file protection, Keychain access semantics, authentication/session lifecycle, background snapshots, clipboard behavior, deep links, app extensions, WebView boundaries and API traffic. Use runtime instrumentation only within the specifically authorized test process and device.

### 4. Data protection and privacy

Assess encryption at rest, key lifecycle, backup/iCloud behavior, device lock state handling, biometric gating, analytics/telemetry, permission minimization, retention and account deletion. Distinguish platform-provided guarantees from application-enforced controls.

### 5. API and identity integration

Use the API security testing and enterprise identity review workflows for backend authorization, OAuth/OIDC, session and identity controls. Use synthetic users and records only.

### 6. Validate and report

Map findings to MASVS controls, MASTG tests and MASWE weaknesses. Record build type/signing context, device model and OS, reproduction conditions, expected/observed behavior, sanitized evidence and remediation. Validate the fixed release on the same device/OS combination.

## Output

Binary/build inventory, signing and entitlement review, static-analysis findings, runtime observations, Keychain/data-protection review, network/privacy notes, MASVS/MASTG/MASWE mapping and remediation plan.

## Safety constraints

- Do not bypass device security for access to unrelated data or apps.
- Do not collect personal Apple IDs, iCloud tokens, Keychain contents, certificates or production user records.
- Do not re-sign, redistribute, patch or publish client binaries without permission.
- Do not test App Store or third-party apps outside the approved account and scope.
- Stop if a test device contains real personal data or the app connects to production services unexpectedly.

## References

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)
- [OWASP MASWE](https://mas.owasp.org/MASWE/)
- [Apple Platform Security](https://support.apple.com/guide/security/welcome/web)
- [Apple App Security](https://developer.apple.com/documentation/security)
- [Frida](https://frida.re/docs/)
