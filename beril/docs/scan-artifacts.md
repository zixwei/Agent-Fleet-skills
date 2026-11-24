# Artifact scans

`beril scan` works on files and folders as well as URLs. It is static and read-only. Nothing is installed, executed, or uploaded.

```bash
./beril scan --target ./build/app.apk
./beril scan --target ./source --tools
./beril scan --target ./bin/service
./beril scan --target ./release/app.exe
```

The scanner detects the type and runs the matching checks. Use it on an artifact you own or are authorized to review.

## What each type checks

| Type | Checks |
|---|---|
| Source tree | Secrets, dangerous sinks, debug flags, CI permissions, container settings, public cloud resources, lockfiles |
| APK | Manifest flags, exported components, permissions, debug certificate, cleartext, backup, native ABIs, embedded strings |
| IPA | Info.plist, ATS, URL schemes, frameworks, embedded strings |
| PE / EXE / DLL | ASLR, DEP, CFG, code signature, section entropy, RWX sections, suspicious imports, packer markers |
| ELF | PIE, NX, RELRO, canary, RPATH, SUID, imports, embedded URLs |
| Archive | Path traversal, symlinks, huge entries, nested artifacts, secrets in text entries |

Findings use critical to info. The score is a rough triage number, not CVSS.

## Source review

Point the scanner at the repository root.

```bash
./beril scan --target ./source --tools
```

The built-in pass looks for:

- cloud keys, private keys, database URLs, tokens, and generic secret assignments;
- `eval`, `exec`, `pickle`, `unserialize`, `innerHTML`, `child_process`, and similar sinks;
- `DEBUG=True`, wildcard allowed hosts, wildcard CORS, disabled TLS checks;
- `pull_request_target`, `write-all`, unpinned actions;
- `FROM ...:latest`, remote scripts piped to a shell, privileged containers;
- public S3 ACLs, `0.0.0.0/0`, public databases;
- a tracked `.env` and missing lockfiles.

`--tools` adds semgrep, gitleaks, and trivy when they are installed.

Triage tips:

1. A hit in a test fixture or a documentation example is usually noise.
2. Confirm a secret with a read-only API call before reporting it.
3. Follow a sink to the source of its input. A hit without a data flow is a hint.
4. Check whether the debug flag applies to production config, not only to `settings/dev.py`.

## APK review

```bash
./beril scan --target ./app-release.apk
```

The scanner reads the binary manifest and reports:

- debuggable builds and old target SDKs;
- exported activities, services, receivers, and providers;
- dangerous permissions;
- `allowBackup` and cleartext traffic;
- debug signing certificates;
- native ABIs and embedded URLs or secrets.

An exported component is not automatically a bug. Confirm that it accepts input from another app and that the input reaches something sensitive. Main launcher activities are usually exported by design.

For deeper work:

```bash
apktool d app.apk -o app-src
jadx-gui app.apk
apksigner verify --print-certs app.apk
```

Then read the manifest by hand. The scanner cannot follow a deep link into a vulnerable handler.

## IPA review

```bash
./beril scan --target ./app.ipa
```

Checks include App Transport Security exceptions, URL schemes, frameworks, and strings in plists and bundles. A broad `NSAllowsArbitraryLoads` is a lead, not a finding. Confirm which domains actually use HTTP.

For runtime work use a jailbroken device or a corellium-style lab with Frida and objection.

## PE review

```bash
./beril scan --target ./app.exe
```

The scanner reports missing ASLR, DEP, CFG, signatures, high entropy sections, RWX sections, and suspicious imports. A missing signature matters for distribution, not always for exploitation.

Useful follow-ups:

```bash
objdump -p app.exe | head -80
strings -n 8 app.exe | grep -Ei "http|cmd|powershell|token"
```

For a deep reverse, load the binary in Ghidra or `r2 -A`.

## ELF review

```bash
./beril scan --target ./bin/service
```

The scanner reports PIE, NX, RELRO, canary, RPATH, SUID, imports, and embedded URLs. A missing canary matters only when a stack buffer is reachable from input.

Follow-ups:

```bash
checksec --file=./bin/service
readelf -hW ./bin/service
readelf -lW ./bin/service | grep GNU_STACK
nm -D ./bin/service
```

## Archive review

```bash
./beril scan --target ./release.zip
```

Checks for `../` entries, absolute paths, symlinks, very large entries, nested apps, and secrets in text files. A traversal entry matters when the extraction tool follows it and a service later reads the extracted path.

## Optional tools

```bash
pip install semgrep
go install github.com/zricethezav/gitleaks/v8@latest
go install github.com/aquasecurity/trivy/cmd/trivy@latest
sudo apt install apktool jadx
pip install frida-tools objection
```

`./install.sh --tools` installs gitleaks, trivy, and semgrep along with the recon tools.

The scanner merges their findings as `semgrep`, `gitleaks`, and `trivy` entries. Treat those as leads. A SAST rule can fire on safe code, and a dependency CVE needs a reachability check.

## Reading the output

```text
scans/app-release.md
scans/app-release.json
```

The Markdown groups findings by severity and gives evidence and advice. The JSON is for a pipeline.

| Severity | Typical action |
|---|---|
| Critical | Live secret, debug signing on a release, public bucket with data |
| High | Exported service without permission, no ASLR, tracked `.env` |
| Medium | Debuggable build, missing RELRO, cleartext allowed |
| Low | Missing lockfile, unpinned base image |
| Info | Permission, technology, or context worth knowing |

## Limits

- Nothing is executed, so packers, dynamic loading, and runtime permissions are invisible.
- Obfuscated strings need a real reverse-engineering session.
- Pattern matches can flag placeholders such as `AKIAIOSFODNN7EXAMPLE`.
- Source scan skips `node_modules`, `vendor`, `dist`, `build`, `.venv`, and similar directories.
- Large files are sampled, not fully read.
- A clean result is not a certification.

## Safety

Static analysis does not touch the network. `--tools` may run local binaries only. If a source tree contains a huge dependency folder, the default file cap keeps the scan bounded.

Authorization still applies. A local copy does not grant the right to test the running service.
