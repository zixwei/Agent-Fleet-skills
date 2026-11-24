---
name: desktop-application-security
description: Assess authorized Windows, macOS and Linux desktop/thick-client applications including Electron, .NET, Java and native apps for local storage, IPC, update, signing, privilege and network security weaknesses.
---

# Desktop application security assessment

## Purpose

Review desktop and thick-client software, including native apps, Electron, .NET/WPF/WinUI, Java, Qt and cross-platform runtimes. Assess local privilege boundaries, IPC, update channels, secrets storage, protocol handlers, application signing and API behavior.

## Prerequisites

- Written authorization identifies application/build hash, supported OS versions, test devices, source/build pipeline, backend endpoints and test identities.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use isolated test VMs or dedicated test workstations with snapshots and synthetic data.
- Confirm whether installers, auto-updaters, privileged helpers, IPC services, browser extensions and plugins are in scope.

## Tools

- **Ghidra:** static binary analysis. [Project](https://github.com/NationalSecurityAgency/ghidra)
- **Sysinternals Process Monitor / Autoruns / Sigcheck:** inspect Windows process, startup and signing behavior. [Suite](https://learn.microsoft.com/sysinternals/)
- **Microsoft WinDbg:** debug owned test binaries. [Documentation](https://learn.microsoft.com/windows-hardware/drivers/debugger/)
- **Objective-See tools:** macOS endpoint and application security inspection. [Project](https://objective-see.org/)
- **strace / ltrace:** observe Linux process interactions in a local lab. [Manual](https://man7.org/linux/man-pages/man1/strace.1.html)
- **Electron security guidance:** review application packaging and secure defaults. [Guide](https://www.electronjs.org/docs/latest/tutorial/security)
- **Semgrep / Gitleaks / Trivy:** source, secrets and dependency analysis. [Semgrep](https://semgrep.dev/docs/) · [Gitleaks](https://github.com/gitleaks/gitleaks) · [Trivy](https://trivy.dev/latest/)

## Workflow

### 1. Inventory build and trust

Record product/version, installer hash, signing identity/certificate chain, update URL, architecture, runtime, native modules, privileged helpers, installed services, auto-start items and supported OS lifecycle.

### 2. Review installation and update behavior

Inspect installer privileges, update signature verification, transport, rollback behavior, downgrade protection, package integrity, update channel permissions, temporary files and post-install actions. Use a local mock update service for dynamic validation; do not intercept or replace production update feeds.

### 3. Review local data and secrets

Inspect configuration, caches, logs, databases, credential storage, crash reports and temp files using synthetic accounts. Check file permissions, per-user boundaries, encryption and secure deletion expectations. Never copy real personal files or credential stores.

### 4. Review IPC and protocol handlers

For Electron, inspect context isolation, sandbox, Node integration, preload bridges, navigation, custom protocols, permissions and remote content. For native apps, review named pipes, Unix sockets, D-Bus, XPC, COM, localhost services, URL/file associations and plugin interfaces. Check caller authentication and message validation using the test harness.

### 5. Review privilege and process boundaries

Assess privileged helper services, service accounts, elevation prompts, install/update permissions, code signing, sandboxing, child-process creation, environment inheritance and writable executable/search paths. Do not attempt privilege escalation or persistence on a production endpoint.

### 6. Review browser/network and API behavior

Inspect TLS, certificate validation, proxy behavior, embedded browser settings, cookie/session storage, deep links and API authorization. Use a test backend and the API/identity skills as needed.

### 7. Review dependencies and release pipeline

Inspect runtime versions, native libraries, plugins, package lockfiles, SBOM, signing keys, reproducible build controls and release artifact provenance. Treat source maps and debug symbols as potentially sensitive artifacts.

### 8. Validate and report

Record OS/build, app hash/signing, local account context, IPC boundary, exact file/registry/service setting, reproduction conditions, evidence and remediation. Confirm findings on the fixed build.

## Output

Application and installer inventory, signing/update review, data storage findings, IPC/protocol assessment, privilege boundary review, network/API results, supply-chain map and remediation priorities.

## Safety constraints

- Use isolated test VMs and synthetic accounts.
- Do not modify system services, registry, launch agents, update feeds or user profiles during assessment.
- Do not access credential stores, personal documents or unrelated processes.
- No persistence, stealth, malware installation, unauthorized shell execution or production update interception.
- Stop if the app or test harness reaches out-of-scope systems or alters workstation state.

## References

- [Electron Security](https://www.electronjs.org/docs/latest/tutorial/security)
- [Microsoft Sysinternals](https://learn.microsoft.com/sysinternals/)
- [Microsoft Windows App Security](https://learn.microsoft.com/windows/apps/security/)
- [Apple Platform Security](https://support.apple.com/guide/security/welcome/web)
- [Ghidra](https://github.com/NationalSecurityAgency/ghidra)
