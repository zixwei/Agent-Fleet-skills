# Tooling

A tool list is not a methodology. This file maps tools to a phase, gives the command that does the first useful thing, and names the trap that wastes time.

## Selection rules

1. One tool per job. Two subdomain enumerators add a little coverage and a lot of duplicates.
2. Prefer tools that read lines and write lines. They compose and their output is easy to diff.
3. Check the last release. A popular repo with no commits in three years may still work, but test it before a live engagement.
4. Keep raw output. Normalize into a second file. You will need the original when a finding looks wrong.
5. Respect rate limits. A scanner that ignores `Retry-After` gets your IP blocked and misses the asset behind the block.
6. Install the smallest set that answers the question. A full recon framework is overhead on a one-host target.
7. Read the help. Flags change between major versions.

## Phase map

| Phase | Job | Default tools |
|---|---|---|
| Scope | Turn the program brief into an asset list | Spreadsheet, `asnmap`, `whois`, certificate search |
| Passive recon | Find names without touching the target | `subfinder`, `amass`, `assetfinder`, `chaos`, `gau`, `waybackurls` |
| Active recon | Resolve, probe, fingerprint | `dnsx`, `httpx`, `tlsx`, `cdncheck`, `naabu`, `nmap` |
| Content discovery | Find paths, files, parameters | `ffuf`, `feroxbuster`, `gobuster`, `dirsearch`, `arjun`, `katana` |
| Vulnerability scan | Match known patterns | `nuclei`, `afrog`, `nikto`, `wpscan`, `xray` |
| Manual web testing | Prove logic and parser bugs | Burp Suite, `sqlmap`, `dalfox`, `jwt_tool`, `graphql-cop` |
| Cloud and AD | Move through identity and network | `bloodhound`, `impacket`, `certipy`, `rubeus`, `chisel`, `ligolo-ng` |
| Mobile | Inspect and hook apps | MobSF, `apktool`, `jadx`, Frida, objection |
| Binary | Analyze and exploit | `checksec`, Ghidra, `radare2`, `pwntools`, ROPgadget |
| OSINT | Map people and infrastructure | `sherlock`, `maigret`, `theHarvester`, SpiderFoot, `web-check` |
| Audit | Review code and dependencies | `semgrep`, `trivy`, `grype`, `gitleaks`, `trufflehog`, `lynis` |
| Report | Turn evidence into a finding | Markdown, screenshots, CVSS calculator |

## Recon

### Subdomain discovery

```bash
subfinder -d example.com -all -silent -o subs.txt
amass enum -passive -d example.com -o amass.txt
cat subs.txt amass.txt | tr 'A-Z' 'a-z' | sort -u > all-subs.txt
```

`subfinder -all` uses every configured source and is slower. Start without `-all` when you need a fast first pass.

Permutation and brute force:

```bash
alterx -l all-subs.txt -o permutations.txt
dnsx -l permutations.txt -a -resp -silent -o resolved.txt
puredns bruteforce wordlist.txt example.com -r resolvers.txt -w wildcard.txt
```

Trap: wildcard DNS makes every random name resolve. Always run a wildcard check before trusting brute force results.

### Probing and fingerprinting

```bash
httpx -l all-subs.txt -sc -title -tech-detect -cdn -follow-redirects -o live.txt
tlsx -l live.txt -san -cn -tls-version -o tls.txt
cdncheck -l live.txt -resp -o cdn.txt
```

`httpx` output is the base for everything else. Keep the status code, title, content length, and technology columns. A host with a 200 and a login title is more interesting than a 200 and a parking page.

Ports:

```bash
naabu -l live.txt -top-ports 1000 -silent -o ports.txt
nmap -sV -sC -Pn -iL live.txt -oA nmap-scan
```

Use `naabu` for speed and `nmap` for service detail. Do not run both against the same host at the same time.

### URL and parameter collection

```bash
cat all-subs.txt | waybackurls | anew urls.txt
gau example.com --threads 5 | anew urls.txt
katana -u https://example.com -jc -kf all -d 3 -o crawl.txt
cat urls.txt crawl.txt | unfurl -u keys | sort -u > params.txt
```

`unfurl` is the cleanest way to extract keys, paths, and domains from a URL list. `anew` appends only new lines, which keeps long-running collection idempotent.

Trap: Wayback URLs can point to deleted endpoints with old behavior. Treat them as leads, then check whether the endpoint still exists.

### ASN and infrastructure

```bash
asnmap -d example.com -json -o asn.json
asnmap -asn AS13335 -json | jq -r '.prefixes[]' > cidrs.txt
```

Use ASN data to find hosts outside the program's main domain. Check the scope before scanning them.

## Content discovery

```bash
ffuf -u https://example.com/FUZZ -w /path/to/wordlist \
  -mc 200,204,301,302,307,401,403 -ac -t 40 -o ffuf.json
feroxbuster -u https://example.com -w /path/to/wordlist -d 2 --smart -o ferox.txt
gobuster dir -u https://example.com -w /path/to/wordlist -x php,html,json
```

Start with a small, high-signal wordlist. Use `-ac` in ffuf to filter responses that look like the catch-all page. Check 401 and 403 paths by hand; they often hide the interesting application.

Parameter discovery:

```bash
arjun -u https://example.com/api/endpoint -m GET,POST
python3 paramspider.py -d example.com
```

Trap: a parameter that changes the response size is not automatically a bug. Confirm the behavior with two values and a clean baseline.

## Web testing

### SQL injection

```bash
sqlmap -u "https://example.com/item?id=1" --batch --risk=2 --level=3
sqlmap -r request.txt -p id --dbms=mysql --technique=BEUSTQ
```

Use a saved request when the endpoint needs cookies or JSON. Start with `--level=2 --risk=2`; higher levels are noisy and slow.

### XSS

```bash
dalfox url "https://example.com/search?q=test" --deep-domxss
python3 XSStrike -u "https://example.com/search?q=test"
```

For DOM XSS, use the browser. A payload that fires in a headless DOM may not fire after the framework re-renders.

### Nuclei

```bash
nuclei -update-templates
nuclei -l live.txt -tags cve,exposure,misconfig -severity high,critical -o nuclei.txt
nuclei -u https://example.com -t http/cves/ -stats
```

Templates are signatures. A clean nuclei run does not mean the target is clean. It means the known signatures did not match.

### WordPress

```bash
wpscan --url https://example.com --enumerate vp,vt,u --api-token "$WPSCAN_TOKEN"
```

The API token is free and gives vulnerability data for plugins. Without it you get version numbers and user names only.

### API and GraphQL

```bash
python3 graphql-cop.py -t https://example.com/graphql
clairvoyance -o schema.json https://example.com/graphql
python3 jwt_tool.py "$TOKEN" -M at
```

For REST APIs, collect the OpenAPI or Swagger file first. Then test object authorization with two accounts. Tools find schema issues; the authorization bug usually needs manual requests.

### Request smuggling and cache

Use Burp Suite with HTTP/1.1 and HTTP/2. The manual sequence matters more than the tool:

1. Send a request with both `Content-Length` and `Transfer-Encoding`.
2. Compare front-end and back-end behavior.
3. Test CL.TE, TE.CL, TE.TE, and HTTP/2 downgrade variants.
4. Confirm with a timing delay or a poisoned response.

`turbo-intruder` is the tool for race conditions and single-packet attacks.

## Cloud and Active Directory

### AD collection

```bash
bloodhound-python -u user -p 'password' -d CORP.LOCAL -ns 10.0.0.10 -c All
impacket-GetUserSPNs CORP.LOCAL/user:password -dc-ip 10.0.0.10 -request
kerbrute userenum --dc 10.0.0.10 -d CORP.LOCAL users.txt
certipy find -u user@corp.local -p 'password' -dc-ip 10.0.0.10
```

`certipy find` output is the fastest way to spot ADCS misconfigurations. Read the ESC1 to ESC8 explanations before exploiting.

Kerberoast and AS-REP:

```bash
Rubeus.exe kerberoast /outfile:hashes.txt
impacket-GetNPUsers CORP.LOCAL/ -dc-ip 10.0.0.10 -usersfile users.txt -no-pass
```

Pivoting:

```bash
chisel server -p 8080 --reverse
chisel client 10.0.0.5:8080 R:socks
ligolo-ng -selfcert
```

Trap: a hash is not a finding. Crack it, show the access it gives, and stop at the agreed proof.

### Cloud

Check the metadata service from an SSRF or a foothold:

```bash
curl -s http://169.254.169.254/latest/meta-data/
curl -s -H "Metadata-Flavor: Google" http://metadata.google.internal/computeMetadata/v1/
```

For AWS, review the role policy before assuming the credentials are powerful. `aws sts get-caller-identity` is the first call.

## Mobile

```bash
docker run -p 8000:8000 opensecurity/mobile-security-framework-mobsf:latest
apktool d app.apk -o app-src
jadx-gui app.apk
frida -U -f com.example.app -l hook.js --no-pause
objection -g com.example.app explore
```

Look for exported activities, deep links, hardcoded keys, weak certificate pinning, and WebView bridges. Use Frida to confirm a runtime behavior, not only a string in the APK.

## Binary

```bash
checksec --file=./challenge
ROPgadget --binary ./challenge --only "pop|ret"
one_gadget libc.so.6
r2 -A ./challenge
ghidraRun
```

A typical remote stack overflow workflow:

1. Find the offset with a cyclic pattern.
2. Identify the mitigation set.
3. Leak a code or libc address if PIE or ASLR is on.
4. Build the ROP chain.
5. Test locally under the same libc.
6. Run remotely and keep the exploit script.

The 0x00sec series in the snapshot walks through a PIE bypass with a format string leak, then a ret2plt chain. Read it for the offset math and the socket reuse detail.

## OSINT

```bash
sherlock username
maigret username --html
theHarvester -d example.com -b all
phoneinfoga scan -n "+15551234567"
ghunt email@example.com
python3 sf.py -l 127.0.0.1:5001
docker run -p 3000:3000 lissy93/web-check
```

Use OSINT to enrich an asset or a person, not to dox. Keep the output to what the engagement needs.

## Audit and supply chain

```bash
semgrep --config auto .
trivy fs --scanners vuln,secret,misconfig .
grype dir:.
gitleaks detect --source . -v
trufflehog git file://. --only-verified
lynis audit system
```

`--only-verified` in trufflehog is important. Unverified secrets create noise and can expose a live credential in a report.

## Agentic pentest tools

```bash
npx @keygraph/shannon@latest
strix --target https://example.com
pentestgpt --target https://example.com
go install github.com/xalgord/xalgorix/v4/cmd/xalgorix@latest
cyberstrike
```

Use these as a second pair of eyes. Read their findings, replay every request, and keep only what you can reproduce. An agent's report is a lead, not evidence.

## Tool chains

### New external domain, 30 minutes

```bash
subfinder -d example.com -silent | anew subs.txt
httpx -l subs.txt -sc -title -tech-detect -o live.txt
nuclei -l live.txt -severity high,critical -tags cve -o quick.txt
```

Then open the five most interesting titles in a browser. Automation narrows the field; manual review finds the bug.

### API target

```bash
katana -u https://api.example.com -jc -d 3 -o api-urls.txt
cat api-urls.txt | unfurl -u paths | sort -u
arjun -u https://api.example.com/v1/user -m GET,POST
```

Find the docs, then test object-level authorization with two accounts.

### WordPress target

```bash
wpscan --url https://example.com --enumerate vp,vt,u --api-token "$WPSCAN_TOKEN"
nuclei -u https://example.com -tags wordpress
```

Check XML-RPC, user enumeration, and plugin versions. A plugin CVE needs the exact version and a reachable endpoint.

### Internal AD

```bash
nmap -sV -Pn -p 88,389,445,3389,5985 10.0.0.0/24
bloodhound-python -u user -p pass -d CORP.LOCAL -ns 10.0.0.10 -c All
certipy find -u user@corp.local -p pass -dc-ip 10.0.0.10
```

Map the path before exploiting. A slow, quiet path is better than a loud scan that trips detection.

### Source code review

```bash
semgrep --config auto .
gitleaks detect --source . -v
trivy fs .
```

Then grep for the framework's dangerous sinks. For Node: `child_process`, `eval`, `vm`, `fs`. For Python: `pickle`, `yaml.load`, `subprocess`, `os.system`. For PHP: `include`, `unserialize`, `eval`.

## Artifact analysis

Use these when `./beril scan` flags an artifact and you need to go deeper.

### APK

```bash
apktool d app.apk -o app-src
jadx-gui app.apk
apksigner verify --print-certs app.apk
aapt dump badging app.apk
```

MobSF gives a full report in a browser. Frida and objection are for runtime work on a rooted device or emulator.

### IPA

```bash
unzip -l app.ipa
plutil -p Payload/App.app/Info.plist
otool -L Payload/App.app/App
```

On Linux, MobSF and a plist parser cover most of the static pass.

### PE

```bash
objdump -p app.exe | head -80
strings -n 8 app.exe | grep -Ei "http|cmd|powershell|token"
```

Ghidra, `r2 -A`, FLOSS, capa, and PEStudio are the deeper tools. Check the signature with `osslsigncode verify`.

### ELF

```bash
checksec --file=./bin/service
readelf -hW ./bin/service
readelf -lW ./bin/service | grep GNU_STACK
readelf -dW ./bin/service | grep -E "RPATH|RUNPATH|BIND_NOW"
```

Ghidra and `r2 -A` for control flow. `pwntools` for offsets and exploit scripts.

### Source

```bash
semgrep --config auto .
gitleaks detect --source . --no-git -v
trufflehog filesystem . --only-verified
trivy fs --scanners vuln,secret,misconfig .
grype dir:.
```

CodeQL adds data-flow queries when the language is supported. Dependency scanners need a lockfile to be useful.

### Archives

```bash
unzip -l release.zip
7z l release.7z
binwalk release.bin
```

Extract into a clean directory, then scan the extracted tree. Never extract an untrusted archive as root.

## Output hygiene

```bash
sort -u raw.txt | anew clean.txt
awk '{print $1}' live.txt | sort -u > hosts.txt
```

Name files with the phase and date: `2026-11-22_recon_subs.txt`. Keep a short `notes.md` with the command, the time, and the result count. When a finding is challenged, the log is the difference between a report and a guess.
