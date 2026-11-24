---
name: cross-platform-mobile-security
description: Assess authorized Flutter, React Native, .NET MAUI and other cross-platform mobile applications for framework-specific storage, bridge, plugin, lifecycle, network and supply-chain security weaknesses.
---

# Cross-platform mobile application security assessment

## Purpose

Review mobile applications built with Flutter, React Native, .NET MAUI, Xamarin or other cross-platform frameworks. Separate framework-specific risks from native Android/iOS platform controls and backend/API behavior.

## Prerequisites

- Written authorization names the app, framework/version, exact Android/iOS builds, source repository, test devices, API endpoints and test accounts.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Obtain source maps/symbols and dependency lockfiles where possible.
- Use dedicated devices/emulators and synthetic accounts. Confirm whether the framework runtime and native plugins may be instrumented.

## Tools

- **MobSF:** inspect packaged Android/iOS artifacts. [Project](https://github.com/MobSF/Mobile-Security-Framework-MobSF)
- **Flutter DevTools:** inspect owned debug builds and runtime performance/behavior. [Documentation](https://docs.flutter.dev/tools/devtools)
- **Dart and Flutter analyzer:** review source, lints and package usage. [Documentation](https://dart.dev/tools/dart-analyze)
- **React Native DevTools:** inspect owned development builds. [Documentation](https://reactnative.dev/docs/react-native-devtools)
- **Semgrep:** source analysis with rules reviewed for the relevant language/framework. [Documentation](https://semgrep.dev/docs/)
- **Frida:** controlled runtime observation. [Documentation](https://frida.re/docs/)
- **Gitleaks / Trivy:** local repository secret and dependency checks. [Gitleaks](https://github.com/gitleaks/gitleaks) · [Trivy](https://trivy.dev/latest/)

## Workflow

### 1. Identify framework and build chain

Identify framework/runtime version, native platform wrappers, build flavors, plugins/packages, native modules, OTA update mechanism, signing and release pipeline. Pin all lockfiles and artifact hashes.

### 2. Review framework-specific storage

For Flutter, review secure-storage packages, platform-channel code, local databases, shared preferences, files, caches and cryptographic package choices. For React Native, review AsyncStorage, secure-storage modules, native bridge methods, JavaScript bundle exposure and debug tooling. For MAUI/Xamarin, review SecureStorage, Preferences, WebView and platform-specific handlers.

Verify the actual platform storage implementation; a package claiming to be secure does not guarantee correct Keychain/Keystore semantics.

### 3. Review bridges, plugins and native APIs

Map Dart platform channels, React Native native modules/bridges, MAUI handlers and plugin method exposure. Check argument validation, caller assumptions, exported Android components, iOS URL handling, permissions, WebViews, deep links and native callback boundaries.

### 4. Review application and runtime configuration

Check debug/profile/release separation, developer menus, source maps, logging, crash reports, obfuscation/symbol retention, cleartext traffic, certificate validation, deep-link routing and runtime update integrity. Do not modify production builds or disable endpoint defenses.

### 5. Review dependencies and supply chain

Inspect package registries, transitive dependencies, abandoned plugins, native SDK binaries, update cadence, license constraints and reproducible build evidence. Verify the resolved dependency graph rather than only top-level package names.

### 6. Review backend and identity interactions

Trace API requests, auth token lifecycle, refresh storage, logout, account switching, authorization and error handling. Use approved test users and synthetic objects. Delegate API/identity-specific checks to their dedicated Pentestizer skills.

### 7. Compare Android and iOS behavior

Test the same security requirement on both platforms. Record behavior differences in Keychain versus Keystore, backup, permissions, app links, background behavior and network policy.

### 8. Validate and report

Map native findings to MASVS/MASTG/MASWE and source findings to CWE/ASVS where applicable. Record the framework, package version, platform, build hash, source path, native dependency and remediation.

## Output

Framework/build inventory, dependency and plugin map, storage/bridge findings, native platform comparison, network/authentication findings, supply-chain observations and prioritized remediation.

## Safety constraints

- Do not extract live tokens, cookies, private keys or user data.
- Do not bypass app-store controls, tamper with production update channels or distribute altered client apps.
- Instrument only owned test builds and devices. Keep production builds untouched unless specific written approval exists.
- Stop if a plugin invokes external production services or if real user data becomes accessible.

## References

- [OWASP MASVS](https://mas.owasp.org/MASVS/)
- [OWASP MASTG](https://mas.owasp.org/MASTG/)
- [Flutter DevTools](https://docs.flutter.dev/tools/devtools)
- [Dart Analyzer](https://dart.dev/tools/dart-analyze)
- [React Native DevTools](https://reactnative.dev/docs/react-native-devtools)
- [Microsoft .NET MAUI security guidance](https://learn.microsoft.com/dotnet/maui/security/)
