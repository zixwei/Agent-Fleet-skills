# One-click use

Beril is built so a person who does not live in a terminal can still get a useful result. This page is the short path.

## 1. Install

```bash
git clone https://github.com/sheduxdev/beril.git
cd beril
./install.sh
```

The installer creates a Python environment, installs the packages, downloads the Camoufox browser, and checks the catalog. It needs about 300 MB of disk and an internet connection.

Optional recon tools:

```bash
./install.sh --tools
```

That installs the ProjectDiscovery tools, ffuf, gau, waybackurls, assetfinder, gobuster, and hakrawler if Go is available. It is optional. The scanner works without them.

## 2. Scan your application

```bash
./beril scan --target https://your-app.example
```

The same command works on local artifacts:

```bash
./beril scan --target ./app.apk
./beril scan --target ./app.exe
./beril scan --target ./bin/service
./beril scan --target ./source --tools
```

APK, IPA, PE, ELF, archive and source checks are static. Details are in `scan-artifacts.md` and `reverse-engineering.md`.

For a binary that needs deeper work:

```bash
./install.sh --re
./beril scan --target ./sample --deep
```


If you leave off the target, Beril asks for it:

```bash
./beril scan
```

Or install and scan in one command:

```bash
./install.sh --scan https://your-app.example
```

The scanner makes read-only requests:

- the page itself, its headers, and its title;
- TLS version, issuer, and certificate expiry;
- security headers and cookie flags;
- a short list of paths that are often left exposed: `.git`, `.env`, backups, `phpinfo`, Spring Actuator, OpenAPI, Swagger, GraphQL, `security.txt`;
- basic HTML fingerprints and password forms served over HTTP.

It does not log in, fuzz, inject payloads, brute force, or execute a local binary. Run it only on an app you own or are authorized to test.

Output:

```text
scans/your-app_example.md
scans/your-app_example.json
```

Open the Markdown file. The top table gives severity counts, then each finding lists evidence and a fix. A clean pass means the short list passed, not that the app is secure.

## 3. Add a known-CVE pass

If nuclei is installed, run it yourself against the same target:

```bash
nuclei -u https://your-app.example -severity medium,high,critical \
  -tags cve,exposure,misconfig -o scans/nuclei.txt
```

Read every match. A nuclei hit is a signature, not proof. Replay the request before you report it.

## 4. Read the result like a human

| Severity | What to do |
|---|---|
| Critical | Fix today. Check whether the exposed file contains live credentials. |
| High | Fix this week. Confirm the exposure with a second request. |
| Medium | Put it in the next maintenance window. |
| Low | Batch it with other hardening work. |
| Info | Decide whether it is intended. Do not report it as a bug by itself. |

The most common real finding in this scan is an exposed `.env` or `.git` directory. Rotate any secret you find there, even if you delete the file.

## 5. Research the stack

```bash
./beril harvest forums --sources 0x00sec,htb,reddit,hackerone --limit 20
./beril harvest tools --sources canonical --limit 40
./beril harvest methods --limit 15
./beril dossier
```

The dossier lands in `examples/dossier.md`. Use it to find the tools and methods that match your stack.

## 6. Refresh

```bash
./beril harvest all --out-dir data --limit 20
./beril validate
```

Monthly is enough for tools. Weekly is better for disclosed reports and forum threads.

## Troubleshooting

| Problem | Fix |
|---|---|
| `camoufox fetch` fails | Check the network or proxy. Run it again. The browser lives in `~/.cache/camoufox`. |
| Challenge page never clears | Use `--headful` under `xvfb-run -a`, and pass a fresh `--profile` directory. |
| `./beril` says it is not installed | Run `./install.sh` from the repo root. |
| Go tools missing | Install Go, then run `./install.sh --tools`. |
| APK manifest unreadable | Confirm it is an unencrypted APK. Split APK bundles need all parts. |
| Source scan is slow | Lower `--max-files`, or exclude generated folders. |
| TLS error on an internal host | Add `--insecure` to the scan. Do not use that flag on a public target without a reason. |
| Scan finds nothing | That is normal for a small app. Run the nuclei pass and a manual review. |

## What the one-click scan cannot see

- Bugs that need a login, a role, or a second account.
- Business logic and race conditions.
- Client-side rendering and JavaScript-only routes.
- Authorization mistakes in API objects.
- Server-side bugs that need payloads.
- Runtime behavior of an app or a packed binary. Local scans are static.
- Obfuscated strings, encrypted resources, and split APK bundles.

Those need the method cards in `references/techniques.md`, `scan-artifacts.md`, and a human tester.

## Safety

The scanner sends GET requests to a short path list. It does not upload, delete, or change data. Still, some apps log 404s as errors or rate-limit repeated requests. Run it outside peak hours if the app is sensitive.

Authorization comes first. A scan is not permission.
