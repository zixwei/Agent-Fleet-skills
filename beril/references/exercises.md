# Exercise index

Short drills by domain. Each has a level and the evidence that proves it. Use them when a week feels abstract, or as a warm-up before a lab.

Level: 1 recall, 2 guided, 3 independent, 4 chained.

## Linux and networking

| # | Task | Level | Evidence |
|---|---|---|---|
| 1 | Find every world-writable file outside `/tmp` | 2 | `find` output with owners |
| 2 | Explain each field in `ls -l` | 1 | Written answer |
| 3 | Trace a process and log every file it opens | 2 | `strace -f` excerpt |
| 4 | Capture a DNS query and the response | 2 | PCAP with annotations |
| 5 | Build a TCP client in Python with a timeout | 2 | Script and output |
| 6 | Explain the difference between a socket and a port | 1 | Written answer |
| 7 | Read a certificate chain and list the failures | 2 | `openssl s_client` output |
| 8 | Set up an SSH tunnel and prove it | 2 | Command and `curl` result |

## Web

| # | Task | Level | Evidence |
|---|---|---|---|
| 9 | Map every parameter in the web lab | 1 | Table |
| 10 | Reproduce the IDOR with two sessions | 2 | Two requests |
| 11 | Turn the reflected XSS into cookie theft | 3 | Listener log |
| 12 | UNION the admin row through the SQLi | 3 | Request and response |
| 13 | Read a file three levels above the web root | 2 | Path and content |
| 14 | Use SSRF to reach the health endpoint | 2 | Request and response |
| 15 | Forge the role cookie and open admin | 1 | Cookie and page |
| 16 | Compare HTML and JSON leakage | 2 | Two responses |
| 17 | Write the IDOR report | 3 | Markdown report |
| 18 | Fix the SQLi and add a test | 3 | Diff and test run |
| 19 | Bypass a 403 with a path normalization trick | 3 | Request pair |
| 20 | Find a cache key mismatch on a lab proxy | 4 | Two responses |
| 21 | Test a GraphQL field authorization with two roles | 3 | Query pair |
| 22 | Chain an open redirect into an OAuth token leak | 4 | Flow log |

## Source and code

| # | Task | Level | Evidence |
|---|---|---|---|
| 23 | Find five dangerous sinks with ripgrep | 2 | Grep output |
| 24 | Trace one source to one sink | 2 | Annotated call path |
| 25 | Run semgrep and classify every hit | 3 | Triage table |
| 26 | Write a Semgrep rule for one pattern | 3 | Rule and match |
| 27 | Find a secret in git history | 2 | Commit and value |
| 28 | Remove a tracked `.env` and rotate the value | 3 | Commands and diff |
| 29 | Add a lockfile and a dependency scan | 2 | CI output |
| 30 | Review a Dockerfile for five issues | 2 | List with fixes |

## Binary and RE

| # | Task | Level | Evidence |
|---|---|---|---|
| 31 | Identify the architecture and mitigations | 1 | `checksec` table |
| 32 | Find the offset to RIP | 2 | Cyclic pattern and GDB |
| 33 | Redirect to `win` | 2 | Payload and flag |
| 34 | Leak the stack with `%p` | 2 | Output |
| 35 | Read the secret with `%s` | 3 | Address and output |
| 36 | Take over the UAF callback | 3 | Payload and flag |
| 37 | Explain the integer wrap | 2 | ASAN report |
| 38 | Recover the RE challenge stages | 3 | Decompiled notes |
| 39 | Patch one branch | 3 | Patched binary |
| 40 | Build a ROP chain for the hardened binary | 4 | PoC and 20 runs |
| 41 | Turn `%n` into a write | 4 | Target and value |
| 42 | Diff two builds and name the fix | 4 | Diff of the changed function |

## Fuzzing and triage

| # | Task | Level | Evidence |
|---|---|---|---|
| 43 | Write the input grammar | 1 | One line |
| 44 | Build a seed corpus | 2 | Files and rationale |
| 45 | Run libFuzzer for 60 seconds | 2 | Log and crash |
| 46 | Read the ASAN top frame | 2 | Annotated frame |
| 47 | Minimize the crash | 3 | Short input |
| 48 | Fix the parser and rerun | 3 | Diff and clean run |
| 49 | Classify five crashes | 3 | Triage table |
| 50 | Write a CASR report | 3 | `.casrep` and summary |

## Mobile and firmware

| # | Task | Level | Evidence |
|---|---|---|---|
| 51 | List exported Android components | 1 | Manifest table |
| 52 | Trigger the deep link | 2 | `am start` output |
| 53 | Call the JS bridge from a page | 3 | Console log |
| 54 | Explain the backup exposure | 2 | Written answer |
| 55 | Extract the firmware image | 2 | unblob summary |
| 56 | Prove the CGI injection | 2 | Request and `uid=` |
| 57 | Explain the unsigned update risk | 2 | Written answer |
| 58 | Find the key and name the blast radius | 2 | Path and impact |
| 59 | Fix the CGI properly | 3 | Diff and test |
| 60 | Run a static scan on the rootfs and triage | 3 | Findings table |

## Cloud and detection

| # | Task | Level | Evidence |
|---|---|---|---|
| 61 | List world-open ports and rank them | 2 | Table |
| 62 | Explain the wildcard IAM blast radius | 2 | Written answer |
| 63 | Read the host mount risk | 2 | Written answer |
| 64 | Pin a base image by digest | 2 | Dockerfile diff |
| 65 | Write a Sigma rule for the CGI injection | 3 | Rule and match |
| 66 | Map three findings to ATT&CK | 2 | IDs and rationale |
| 67 | Find a beacon in a PCAP | 3 | Stream and timing |
| 68 | Write a false-positive analysis | 3 | Paragraph |

## Rust and Ghidra plugins

| # | Task | Level | Evidence |
|---|---|---|---|
| 69 | Identify a Rust binary from panic strings and mangled names | 2 | Strings and demangled sample |
| 70 | Recover a crate name and version from a build path | 2 | Path and version |
| 71 | Install GhidRust in a test Ghidra and apply FunctionID | 3 | Install log and renamed function |
| 72 | Compare GhidRust output with Ghidra's upstream Rust support | 3 | Two decompiler views |
| 73 | Write the Rust slice and trait-object layouts from memory | 2 | Diagram |
| 74 | Map an async state machine to its await points | 4 | State table |

## How to use

1. Pick a level above your comfort zone, not below it.
2. Write the evidence path before you start.
3. Stop at 30 minutes. If stuck, write the blocker and move on.
4. Revisit the blocker the next day.
5. Add the answer to your notes, not only the command.

A drill without evidence is a read. A drill with a request, crash, or diff is practice.
