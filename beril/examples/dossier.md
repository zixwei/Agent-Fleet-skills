# Cyber intelligence dossier

Collected 2026-11-22. Every entry links to its public source. Counts describe this snapshot, not the whole internet.

## Coverage

| Layer | Rows | Sources |
|---|---:|---|
| Threads and reports | 447 | 14 |
| Experts | 94 | 4 |
| Tools | 658 | 2 |
| Methods and research | 42 | 8 |

## High-signal threads

- [Unauthenticated remote process termination of Arc RPC nodes via custom JS tracer (Boa engine) leads to sustained node DoS](https://hackerone.com/reports/3966160)
  program Arc, weakness Uncontrolled Resource Consumption, severity High, bounty $10,000 (HackerOne Hacktivity)
- [A disabled account's API key still works on BMO's Mojolicious /rest/ routes, including for security-group bugs filed after the disable](https://hackerone.com/reports/4000084)
  program Mozilla, weakness Improper Authentication - Generic, severity High, bounty $4,000 (HackerOne Hacktivity)
- [Unauthenticated access to Checkout Blocks employee-only endpoints exposed merchant metadata](https://hackerone.com/reports/3954327)
  program Shopify, weakness Missing Authentication for Critical Function, severity Medium, bounty $3,100 (HackerOne Hacktivity)
- [Evernote(WindowsApp) XSS to RCE](https://hackerone.com/reports/2119149)
  program Evernote, weakness Code Injection, severity High, bounty $2,000 (HackerOne Hacktivity)
- [Revoked deployment collaborator regains access to private comments after active collaborator interaction](https://hackerone.com/reports/3852102)
  program Vercel, weakness Access Control Check Implemented After Asset is Accessed, severity Medium, bounty $1,026 (HackerOne Hacktivity)
- [Burp Community OpenAPI site-map action reads a local schema example through $id rebasing](https://hackerone.com/reports/4000535)
  program PortSwigger Web Security, weakness Path Traversal, severity Medium, bounty $1,000 (HackerOne Hacktivity)

## Experts worth reading

- [fg0x0](https://hackerone.com/fg0x0?type=user) - disclosed HackerOne reports
  - [Connector/ODBC: MADB_Stmt::ArrayOffset is not reset when an exception unwinds out of MADB_StmtExecute, so the next paramset reads out of bounds](https://hackerone.com/reports/3920647)
  - [Connector/ODBC: SqlwcsOctetLen sizes UTF-16 with a byte-oriented helper, reading past the caller's buffer and sending client memory to the server](https://hackerone.com/reports/3920633)
- [James Kettle](https://portswigger.net/research/james-kettle) - HTTP desync, request smuggling, cache poisoning, research automation
  - [HTTP desync attacks](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)
  - [hunting unknown vulnerability classes](https://portswigger.net/research/backslash-powered-scanning-hunting-unknown-vulnerability-classes)
- [sumit-sabhaya](https://hackerone.com/sumit-sabhaya?type=user) - disclosed HackerOne reports
  - [Billion laughs entity expansion in Qt .ts discovery: a crafted ~950 KB file forces ~55 MB of XML expansion per parse, cost scales with crafted file c](https://hackerone.com/reports/4094236)
  - [Incomplete VCS metadata exclusion: `.svn` / `.bzr` bypass CVE-2026-61792 due to missing `VCS_METADATA_DIRS` entries.](https://hackerone.com/reports/4037976)
- [Zakhar Fedotkin](https://portswigger.net/research/zakhar-fedotkin) - SAML, cookie prefixes, WebSocket testing, client-side parser tricks
  - [The Fragile Lock: Novel Bypasses For SAML Authentication](https://portswigger.net/research/the-fragile-lock)
  - [WebSocket Turbo Intruder: Unearthing the WebSocket Goldmine](https://portswigger.net/research/websocket-turbo-intruder-unearthing-the-websocket-goldmine)
- [Gareth Heyes](https://portswigger.net/research/gareth-heyes) - XSS, parser differentials, CSP bypass, client-side exfiltration
  - [CSS:the bomb inside your inbox](https://portswigger.net/research/css-the-bomb-inside-your-inbox)
  - [Splitting the email atom: exploiting parsers to bypass access controls](https://portswigger.net/research/splitting-the-email-atom)
- [0xbs0d27](https://hackerone.com/0xbs0d27?type=user) - disclosed HackerOne reports
  - [WordCamp Meta gpre: password-protected event RSVP list readable without password via /gpre/v1/.../rsvp-responses](https://hackerone.com/reports/4029867)
  - [wordpress.org Rosetta: invite stores unvalidated role — Editor can assign site Administrator despite editable_roles](https://hackerone.com/reports/3942522)

## Tool shelf

### agentic

- [usestrix/strix](https://github.com/usestrix/strix) - 76933 stars. Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. - usestrix/strix
- [KeygraphHQ/shannon](https://github.com/KeygraphHQ/shannon) - 52166 stars. Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and e
- [vxcontrol/pentagi](https://github.com/vxcontrol/pentagi) - 34751 stars. Fully autonomous AI Agents system capable of performing complex penetration testing tasks - vxcontrol/pentagi
- [elementalsouls/Claude-BugHunter](https://github.com/elementalsouls/Claude-BugHunter) - 16707 stars. 83-skill bug bounty and red-team bundle with triage gates and report patterns
- [GreyDGL/PentestGPT](https://github.com/GreyDGL/PentestGPT) - 16543 stars. Automated Penetration Testing Agentic Framework Powered by Large Language Models - GreyDGL/PentestGPT
- [BaaRai/ARTEX](https://github.com/BaaRai/ARTEX) - 15555 stars. Self-hosted multi-agent pentest platform with planner, evidence and scope gates

### audit defense

- [aquasecurity/trivy](https://github.com/aquasecurity/trivy) - 39601 stars. Find vulnerabilities, misconfigurations, secrets, SBOM in containers, Kubernetes, code repositories, clouds and more - a
- [gitleaks/gitleaks](https://github.com/gitleaks/gitleaks) - 31246 stars. Find secrets with Gitleaks 🔑. Contribute to gitleaks/gitleaks development by creating an account on GitHub.
- [trufflesecurity/trufflehog](https://github.com/trufflesecurity/trufflehog) - 29565 stars. Find, verify, and analyze leaked credentials. Contribute to trufflesecurity/trufflehog development by creating an accoun
- [returntocorp/semgrep](https://github.com/returntocorp/semgrep) - 17880 stars. Lightweight static analysis for many languages. Find bug variants with patterns that look like source code. - semgrep/se
- [CISOfy/lynis](https://github.com/CISOfy/lynis) - 16738 stars. Lynis - Security auditing tool for Linux, macOS, and UNIX-based systems. Assists with compliance testing (HIPAA/ISO27001
- [anchore/grype](https://github.com/anchore/grype) - 13319 stars. A vulnerability scanner for container images and filesystems - anchore/grype

### cloud ad

- [jpillora/chisel](https://github.com/jpillora/chisel) - 16803 stars. A fast TCP/UDP tunnel over HTTP. Contribute to jpillora/chisel development by creating an account on GitHub.
- [fortra/impacket](https://github.com/fortra/impacket) - 16293 stars. Impacket is a collection of Python classes for working with network protocols. - fortra/impacket
- [BishopFox/sliver](https://github.com/BishopFox/sliver) - 12393 stars. Adversary Emulation Framework. Contribute to BishopFox/sliver development by creating an account on GitHub.
- [BloodHoundAD/BloodHound](https://github.com/BloodHoundAD/BloodHound) - 10631 stars. Six Degrees of Domain Admin. Contribute to SpecterOps/BloodHound-Legacy development by creating an account on GitHub.
- [HavocFramework/Havoc](https://github.com/HavocFramework/Havoc) - 8505 stars. The Havoc Framework. Contribute to HavocFramework/Havoc development by creating an account on GitHub.
- [lgandx/Responder](https://github.com/lgandx/Responder) - 6689 stars. Responder is a LLMNR, NBT-NS and MDNS poisoner, with built-in HTTP/SMB/MSSQL/FTP/LDAP rogue authentication server suppor

### mobile binary

- [NationalSecurityAgency/ghidra](https://github.com/NationalSecurityAgency/ghidra) - 100895 stars. Ghidra is a software reverse engineering (SRE) framework - NationalSecurityAgency/ghidra
- [skylot/jadx](https://github.com/skylot/jadx) - 51929 stars. Dex to Java decompiler. Contribute to skylot/jadx development by creating an account on GitHub.
- [iBotPeaches/Apktool](https://github.com/iBotPeaches/Apktool) - 27322 stars. A tool for reverse engineering Android apk files. Contribute to iBotPeaches/Apktool development by creating an account o
- [radareorg/radare2](https://github.com/radareorg/radare2) - 25478 stars. UNIX-like reverse engineering framework and command-line toolset - radareorg/radare2
- [frida/frida](https://github.com/frida/frida) - 22885 stars. Main repo for hosting release binaries. Contribute to frida/frida development by creating an account on GitHub.
- [MobSF/Mobile-Security-Framework-MobSF](https://github.com/MobSF/Mobile-Security-Framework-MobSF) - 22290 stars. Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malw

### osint surface

- [sherlock-project/sherlock](https://github.com/sherlock-project/sherlock) - 115488 stars. Hunt down social media accounts by username across social networks - sherlock-project/sherlock
- [soxoj/maigret](https://github.com/soxoj/maigret) - 41134 stars. 🕵️‍♂️ Collect a dossier on a person by username from 6K websites - soxoj/maigret
- [lissy93/web-check](https://github.com/lissy93/web-check) - 35560 stars. 🕵️‍♂️ All-in-one OSINT tool for analysing any website - lissy93/web-check
- [smicallef/spiderfoot](https://github.com/smicallef/spiderfoot) - 24815 stars. SpiderFoot automates OSINT for threat intelligence and mapping your attack surface. - smicallef/spiderfoot
- [mxrch/GHunt](https://github.com/mxrch/GHunt) - 19886 stars. 🕵️‍♂️ Offensive Google framework. Contribute to mxrch/GHunt development by creating an account on GitHub.
- [sundowndev/phoneinfoga](https://github.com/sundowndev/phoneinfoga) - 18647 stars. Information gathering framework for phone numbers. Contribute to sundowndev/phoneinfoga development by creating an accou

### re-agentic

- [Claude-BugHunter](elementalsouls/Claude-BugHunter) - 16707 stars. 83-skill bug bounty and red-team bundle with 681 disclosed-report patterns, triage gates, enterprise identity chains and
- [ARTEX](BaaRai/ARTEX) - 15555 stars. Self-hosted multi-agent pentest platform with planner, workers, evidence-bound findings, scope gates and approval budget

### re-binary

- [lief](lief-project/LIEF) - ? stars. Parse and modify ELF, PE, and Mach-O.
- [pefile](erocarrera/pefile) - ? stars. Python PE parser.
- [pyelftools](eliben/pyelftools) - ? stars. Python ELF and DWARF parser.
- [capstone](capstone-engine/capstone) - ? stars. Disassembly framework.

### re-binja

- [borzacchiello/seninja](borzacchiello/seninja) - 361 stars. symbolic execution plugin for binary ninja
- [fkie-cad/dewolf](fkie-cad/dewolf) - 237 stars. A research decompiler implemented as a Binary Ninja plugin.
- [yrp604/bad64](yrp604/bad64) - 108 stars. Binja Arm64 Disassembler
- [matteyeux/binja-diff](matteyeux/binja-diff) - 100 stars. Binary Ninja diffing tool
- [withzombies/bnil-graph](withzombies/bnil-graph) - 89 stars. A BinaryNinja plugin to graph a BNIL instruction tree
- [Vector35/tanto](Vector35/tanto) - 80 stars. Tantō slices functions into more consumable chunks

### re-bytecode

- [Col-E/Recaf](Col-E/Recaf) - 7481 stars. The modern Java bytecode editor
- [Jon-Becker/heimdall-rs](Jon-Becker/heimdall-rs) - 1648 stars. Heimdall is an advanced EVM smart contract toolkit specializing in bytecode analysis and extracting information from unv
- [mirkosertic/Bytecoder](mirkosertic/Bytecoder) - 965 stars. Framework to interpret and transpile JVM bytecode to JavaScript, OpenCL or WebAssembly.

### re-capability

- [capa](mandiant/capa) - ? stars. Identify capabilities from imports and structure.
- [floss](mandiant/flare-floss) - ? stars. Extract obfuscated and stack strings.

### re-cfg

- [google/binnavi](google/binnavi) - 2883 stars. BinNavi is a binary analysis IDE that allows to inspect, navigate, edit and annotate control flow graphs and call graphs
- [DucPhamNgoc08/CodeVisualizer](DucPhamNgoc08/CodeVisualizer) - 702 stars. CodeVisualizer is a powerful VS Code extension that provides two main visualization capabilities: function-level flowcha
- [danielplohmann/smda](danielplohmann/smda) - 264 stars. SMDA is a minimalist recursive disassembler library that is optimized for accurate Control Flow Graph (CFG) recovery fro
- [ircmaxell/php-cfg](ircmaxell/php-cfg) - 247 stars. A Control Flow Graph implementation in PHP
- [coetaur0/staticfg](coetaur0/staticfg) - 216 stars. Python3 control flow graph generator
- [tech-srl/Nero](tech-srl/Nero) - 208 stars. Code and resources for the paper: "Neural Reverse Engineering of Stripped Binaries using Augmented Control Flow Graphs"

### re-debugger

- [unicorn-engine/unicorn](unicorn-engine/unicorn) - 9601 stars. Unicorn CPU emulator framework (ARM, AArch64, M68K, Mips, Sparc, PowerPC, RiscV, S390x, TriCore, X86)
- [mohitmishra786/low-level-dev-skills](mohitmishra786/low-level-dev-skills) - 298 stars. A curated suite of AI agent skills for systems and low-level programming with C/C++, Rust, and Zig toolchains, covering 
- [x64dbg](x64dbg/x64dbg) - ? stars. Windows user-mode debugger.
- [windbg](Microsoft WinDbg) - ? stars. Windows kernel and user-mode debugger with TTD.
- [pwndbg](pwndbg/pwndbg) - ? stars. GDB plugin for exploit development and heap inspection.
- [gef](hugsy/gef) - ? stars. GDB plugin with exploit-oriented views.

### re-decompiler

- [icsharpcode/ILSpy](icsharpcode/ILSpy) - 27128 stars. .NET Decompiler with support for PDB generation, ReadyToRun, Metadata (&more) - cross-platform!
- [Konloch/bytecode-viewer](Konloch/bytecode-viewer) - 15729 stars. A Java 8+ Jar & Android APK Reverse Engineering Suite (Decompiler, Editor, Debugger & More)
- [jindrapetrik/jpexs-decompiler](jindrapetrik/jpexs-decompiler) - 6062 stars. JPEXS Free Flash Decompiler (FFDec) is open-source Flash SWF decompiler and editor. Extract and edit ActionScript, image
- [charles2gan/GDA-android-reversing-Tool](charles2gan/GDA-android-reversing-Tool) - 4879 stars. the fastest and most powerful android decompiler(native tool working without Java VM) for the APK, DEX, ODEX, OAT, JAR, 
- [zrax/pycdc](zrax/pycdc) - 4678 stars. C++ python bytecode disassembler and decompiler
- [JetBrains/fernflower](JetBrains/fernflower) - 4448 stars. Decompiler from Java bytecode to Java, used in IntelliJ IDEA.

### re-diff

- [secretsquirrel/the-backdoor-factory](secretsquirrel/the-backdoor-factory) - 3446 stars. Patch PE, ELF, Mach-O binaries with shellcode new version in development, available only to sponsors
- [sisong/HDiffPatch](sisong/HDiffPatch) - 2159 stars. a C\C++ library and command-line tools for Diff & Patch between binary files or directories(folder); cross-platform; run
- [mendsley/bsdiff](mendsley/bsdiff) - 2147 stars. bsdiff and bspatch are libraries for building and applying patches to binary files.
- [ddvk/remarkable-hacks](ddvk/remarkable-hacks) - 1772 stars. additional functionality via binary patching
- [jmacd/xdelta](jmacd/xdelta) - 1392 stars. open-source binary diff, delta/differential compression tools, VCDIFF/RFC 3284 delta compression
- [ovobrowsersupport/ovobrowser](ovobrowsersupport/ovobrowser) - 1288 stars. 源码级 Chromium 指纹内核，检测站按普通 Chrome 评分。Source-level Chromium fingerprint kernel that passes bot detection tests. Not JS inje

### re-disassembler

- [ghidra](NationalSecurityAgency/ghidra) - ? stars. NSA reverse-engineering framework with decompiler and headless mode.
- [binaryninja](Vector35/binaryninja-api) - ? stars. Commercial reverse-engineering platform with BNIL and Python API. Headless needs a license.
- [ida](hex-rays/ida) - ? stars. Industry decompiler and plugin ecosystem. IDA Free is limited.
- [rizin](rizinorg/rizin) - ? stars. Command-first binary analysis framework and radare2 fork.
- [radare2](radareorg/radare2) - ? stars. Long-running command-line reverse-engineering framework.

### re-dynamic

- [frida](frida/frida) - ? stars. Dynamic instrumentation toolkit for native and managed code.
- [objection](sensepost/objection) - ? stars. Frida-powered mobile runtime exploration.
- [dynamorio](DynamoRIO/dynamorio) - ? stars. Dynamic binary instrumentation framework.
- [pin](intel/pin) - ? stars. Intel dynamic instrumentation.

### re-firmware

- [reacher-z/Awesome-REA](reacher-z/Awesome-REA) - 3322 stars. Curated tools, case studies and papers for reverse engineering websites, games, apps, binaries, firmware and protocols. 
- [morluto/awesome-reverse-engineering](morluto/awesome-reverse-engineering) - 2273 stars. Curated reverse-engineering tools and learning resources for binaries, apps, firmware, formats, and protocols.
- [firmadyne/firmadyne](firmadyne/firmadyne) - 2129 stars. Platform for emulation and dynamic analysis of Linux-based firmware
- [attify/firmware-analysis-toolkit](attify/firmware-analysis-toolkit) - 1642 stars. Firmware security research platform combining binary analysis, taint tracing, and emulation across IoT, edge AI, mobile 
- [fkie-cad/FACT_core](fkie-cad/FACT_core) - 1476 stars. Firmware Analysis and Comparison Tool
- [platomav/MEAnalyzer](platomav/MEAnalyzer) - 1353 stars. Intel Engine & Graphics Firmware Analysis Tool

### re-fuzz

- [marin-m/pbtk](marin-m/pbtk) - 1700 stars. A toolset for reverse engineering and fuzzing Protobuf-based apps

### re-fuzzing

- [aflpp](AFLplusplus/AFLplusplus) - ? stars. Coverage-guided fuzzer.
- [libfuzzer](llvm/llvm-project) - ? stars. In-process coverage-guided fuzzer.
- [honggfuzz](google/honggfuzz) - ? stars. Coverage-guided fuzzer with hardware feedback.
- [syzkaller](google/syzkaller) - ? stars. Kernel syscall fuzzer.
- [casr](ispras/casr) - ? stars. Crash report collection, deduplication, and severity.
- [nyx](nyx-fuzz/Nyx) - ? stars. Snapshot fuzzing engine for AFL++.

### re-ghidra

- [bethington/ghidra-mcp](bethington/ghidra-mcp) - 6322 stars. Ghidra MCP Server — 200+ MCP tools for AI-powered reverse engineering. GUI plugin + headless server, lazy tool loading, 
- [Dryxio/reagent](Dryxio/reagent) - 2458 stars. Reconstruct and validate C/C++ code from compiled programs with AI.
- [bootleg/ret-sync](bootleg/ret-sync) - 2416 stars. ret-sync is a set of plugins that helps to synchronize a debugging session (WinDbg/GDB/LLDB/OllyDbg2/x64dbg) with IDA/Gh
- [KeenSecurityLab/BinAbsInspector](KeenSecurityLab/BinAbsInspector) - 1685 stars. BinAbsInspector: Vulnerability Scanner for Binaries
- [fr0gger/awesome-ida-x64-olly-plugin](fr0gger/awesome-ida-x64-olly-plugin) - 1609 stars. A curated list of IDA x64DBG, Ghidra and OllyDBG plugins.
- [AllsafeCyberSecurity/awesome-ghidra](AllsafeCyberSecurity/awesome-ghidra) - 1466 stars. A curated list of awesome Ghidra materials

### re-gui

- [cutter](rizinorg/cutter) - ? stars. Qt GUI for rizin.

### re-hex

- [WerWolv/ImHex](WerWolv/ImHex) - 56000 stars. 🔍 A Hex Editor for Reverse Engineers, Programmers and people who value their retinas when working at 3 AM.

### re-ida

- [mrexodia/ida-pro-mcp](mrexodia/ida-pro-mcp) - 13541 stars. AI-powered reverse engineering assistant that bridges IDA Pro with language models through MCP.
- [mandiant/flare-ida](mandiant/flare-ida) - 2452 stars. IDA Pro utilities from FLARE team
- [KasperskyLab/hrtng](KasperskyLab/hrtng) - 1974 stars. IDA Pro plugin with a rich set of features: decryption, deobfuscation, patching, lib code recognition and various pseudo
- [airbus-seclab/bincat](airbus-seclab/bincat) - 1881 stars. Binary code static analyser, with IDA integration. Performs value and taint analysis, type reconstruction, use-after-fre
- [illera88/Ponce](illera88/Ponce) - 1634 stars. IDA 2016 plugin contest winner! Symbolic Execution just one-click away!
- [igogo-x86/HexRaysPyTools](igogo-x86/HexRaysPyTools) - 1544 stars. IDA Pro plugin which improves work with HexRays decompiler and helps in process of reconstruction structures and classes

### re-il2cpp

- [Perfare/Il2CppDumper](Perfare/Il2CppDumper) - 9689 stars. Unity il2cpp reverse engineer
- [SamboyCoding/Cpp2IL](SamboyCoding/Cpp2IL) - 3131 stars. Work-in-progress tool to reverse unity's IL2CPP toolchain.
- [dlang-community/Pegged](dlang-community/Pegged) - 547 stars. A Parsing Expression Grammar (PEG) module, using the D programming language.
- [VBForumsCommunity/sqlite3win32](VBForumsCommunity/sqlite3win32) - 28 stars. VB-friendly sqlite3 library with __stdcall calling convention

### re-lab

- [mandiant/flare-vm](mandiant/flare-vm) - 9343 stars. A collection of software installations scripts for Windows systems that allows you to easily setup and maintain a revers

### re-learning

- [mytechnotalent/Reverse-Engineering](mytechnotalent/Reverse-Engineering) - 15232 stars. A FREE comprehensive reverse engineering tutorial covering x86, x64, 32-bit/64-bit ARM, 8-bit AVR and 32-bit RISC-V arch
- [cirosantilli/x86-assembly-cheat](cirosantilli/x86-assembly-cheat) - 896 stars. MOVED TO: https://cirosantilli.com/linux-kernel-module-cheat/userland-assembly with code at https://github.com/cirosanti
- [ManojKumarPatnaik/Major-project-list](ManojKumarPatnaik/Major-project-list) - 262 stars. A list of practical projects that anyone can solve in any programming language (See solutions). These projects are divid
- [CNTRUN/Termux-command](CNTRUN/Termux-command) - 120 stars. char const* const commands[] = { "aapt", " aapt", " zipalign", "abduco", " abduco", "abook", " abook", "alpine", " alpin

### re-malware

- [rshipp/awesome-malware-analysis](rshipp/awesome-malware-analysis) - 14453 stars. Defund the Police.
- [ytisf/theZoo](ytisf/theZoo) - 13596 stars. A repository of LIVE malwares for your own joy and pleasure. theZoo is a project created to make the possibility of malw
- [qilingframework/qiling](qilingframework/qiling) - 6194 stars. A True Instrumentable Binary Emulation Framework
- [cuckoosandbox/cuckoo](cuckoosandbox/cuckoo) - 5956 stars. Cuckoo Sandbox is an automated dynamic malware analysis system
- [RPISEC/Malware](RPISEC/Malware) - 4587 stars. Course materials for Malware Analysis by RPISEC
- [alexandreborges/malwoverview](alexandreborges/malwoverview) - 4146 stars. Malwoverview is a first response tool for threat hunting across VirusTotal, Hybrid Analysis, URLHaus, Polyswarm, Malshar

### re-mcp

- [rehan-remade/universal-modder](rehan-remade/universal-modder) - 9832 stars. Point Claude at any game. Skills, tools and the fal MCP that let Claude Code mod almost any PC game you own: recon, reve
- [duty1g/x64dbg-mcp-server](duty1g/x64dbg-mcp-server) - 2855 stars. x64dbg-MCP Server is a native MCP (Model Context Protocol) plugin for x64dbg that exposes the debugger's full functional
- [imjustprism/borer](imjustprism/borer) - 29 stars. Blazing fast, Rust-based reverse engineering tool built for humans and AI. Next-generation binary analysis in pure Rust:
- [GeRiY/callgraph](GeRiY/callgraph) - 18 stars. A token-efficient code map for AI coding agents: outline, call graph, "who calls this?" and impact analysis without read

### re-mobile

- [OWASP/mastg](OWASP/mastg) - 13367 stars. The OWASP Mobile Application Security Testing Guide (MASTG) is a comprehensive manual for mobile app security testing an
- [majd/ipatool](majd/ipatool) - 12425 stars. Command-line tool that allows you to search for iOS, iPadOS, tvOS, watchOS, visionOS, and macOS apps on the App Store, a
- [firerpa/lamda](firerpa/lamda) - 8759 stars. Android Full-Stack Device Control Platform: WebRTC/H.264 remote desktop, UI/OCR/image-matching automation, one-click MIT
- [qybaihe/bla](qybaihe/bla) - 6396 stars. 🛡️ BLA (bla-agents / Blind LLM Agents) — Open-source Blue Defense MCP server & CLI protecting JS/TS, Electron ASAR, WASM
- [alphaSeclab/awesome-reverse-engineering](alphaSeclab/awesome-reverse-engineering) - 5224 stars. Reverse Engineering Resources About All Platforms(Windows/Linux/macOS/Android/iOS/IoT) And Every Aspect! (More than 3500
- [Ch0pin/medusa](Ch0pin/medusa) - 2373 stars. Mobile Edge-Dynamic Unified Security Analysis

### re-network

- [wireshark](wireshark/wireshark) - ? stars. Protocol analyzer.
- [mitmproxy](mitmproxy/mitmproxy) - ? stars. Interactive HTTPS proxy.

### re-other

- [BinaryAnalysisPlatform/bap](BinaryAnalysisPlatform/bap) - 2263 stars. Binary Analysis Platform
- [redasm-dev/redasm](redasm-dev/redasm) - 1835 stars. The Open Source Disassembler
- [komorra/NodeEditorWinforms](komorra/NodeEditorWinforms) - 612 stars. Node based user control / editor for Windows Forms
- [babakhani/PersianDate](babakhani/PersianDate) - 607 stars. javascript date library for parsing, validating, manipulating, and formatting persian dates System.
- [ScottDuncanAI/PID-to-Knowledge-Graph](ScottDuncanAI/PID-to-Knowledge-Graph) - 312 stars. P&ID to Knowledge Graph: a Claude skill that turns P&ID PDFs (digital or scanned) into a process knowledge graph of flow
- [ergrelet/cpplumber](ergrelet/cpplumber) - 90 stars. Static analysis tool based on clang, which detects source-to-binary information leaks in C and C++ projects

### re-parser

- [orhun/binsider](orhun/binsider) - 4532 stars. Analyze ELF binaries like a boss 😼🕵️‍♂️
- [taocpp/PEGTL](taocpp/PEGTL) - 2175 stars. Parsing Expression Grammar Template Library
- [JusticeRage/Manalyze](JusticeRage/Manalyze) - 1156 stars. A static analyzer for PE executables.
- [Washi1337/AsmResolver](Washi1337/AsmResolver) - 1139 stars. A library for creating, reading and editing PE files and .NET modules.
- [trailofbits/pe-parse](trailofbits/pe-parse) - 912 stars. Principled, lightweight C/C++ PE parser
- [hasherezade/bearparser](hasherezade/bearparser) - 657 stars. Portable Executable parsing library (from PE-bear)

### re-patching

- [chrisneagu/FTC-Skystone-Dark-Angels-Romania-2020](chrisneagu/FTC-Skystone-Dark-Angels-Romania-2020) - 325 stars. NOTICE This repository contains the public FTC SDK for the SKYSTONE (2019-2020) competition season. If you are looking f
- [Adamkadaban/LearnPwn](Adamkadaban/LearnPwn) - 192 stars. Learn Binary Exploitation with sample problems and solutions

### re-radare

- [hteso/iaito](hteso/iaito) - 1461 stars. This project has been moved to:
- [nowsecure/r2frida](nowsecure/r2frida) - 1457 stars. Radare2 and Frida better together.
- [radareorg/radare2-book](radareorg/radare2-book) - 929 stars. The Official Radare2 Book
- [radareorg/awesome-radare2](radareorg/awesome-radare2) - 738 stars. A curated list of awesome projects, articles and the other materials powered by Radare2
- [aemmitt-ns/radius2](aemmitt-ns/radius2) - 638 stars. radius2 is a fast binary emulation and symbolic execution framework using radare2
- [radareorg/r2ai](radareorg/r2ai) - 546 stars. LLM-based reversing for radare2

### re-rust

- [m4b/goblin](m4b/goblin) - 1574 stars. An impish, cross-platform binary parsing crate, written in Rust
- [falconre/falcon](falconre/falcon) - 594 stars. Binary Analysis Framework in Rust
- [fugue-re/fugue-core](fugue-re/fugue-core) - 186 stars. A binary analysis framework written in Rust.
- [xmoezzz/rfvp](xmoezzz/rfvp) - 156 stars. A Non-Official Rust cross-platform implementation of the FVP engine & IDE
- [JPCERTCC/rust-binary-analysis-research-ja](JPCERTCC/rust-binary-analysis-research-ja) - 73 stars. Rustで作成されたバイナリのリバースエンジニアリング調査レポート
- [JPCERTCC/rust-binary-analysis-research-en](JPCERTCC/rust-binary-analysis-research-en) - 43 stars. Study of binaries created with Rust through reverse engineering

### re-signature

- [yara](VirusTotal/yara) - ? stars. Pattern matching for malware families and packers.

### re-source

- [codeql](github/codeql) - ? stars. Semantic code query engine with data flow.
- [joern](joernio/joern) - ? stars. Code property graph query engine.
- [semgrep](semgrep/semgrep) - ? stars. Fast rule-based static analysis.
- [weggli](weggli/weggli) - ? stars. Fast C/C++ syntax query tool.
- [cppcheck](danmar/cppcheck) - ? stars. C/C++ static analyzer.
- [infer](facebook/infer) - ? stars. Static analyzer for Java, C, C++, and Objective-C.

### re-symbolic

- [ConsenSysDiligence/mythril](ConsenSysDiligence/mythril) - 4278 stars. Mythril is a symbolic-execution-based securty analysis tool for EVM bytecode. It detects security vulnerabilities in sma
- [trailofbits/manticore](trailofbits/manticore) - 3857 stars. Symbolic execution tool
- [JonathanSalwan/VMProtect-devirtualization](JonathanSalwan/VMProtect-devirtualization) - 1555 stars. Playing with the VMProtect software protection. Automatic deobfuscation of pure functions using symbolic execution and L
- [ksluckow/awesome-symbolic-execution](ksluckow/awesome-symbolic-execution) - 1505 stars. A curated list of awesome symbolic execution resources including essential research papers, lectures, videos, and tools.
- [shellphish/driller](shellphish/driller) - 988 stars. Driller: augmenting AFL with symbolic execution!
- [AngoraFuzzer/Angora](AngoraFuzzer/Angora) - 957 stars. Angora is a mutation-based fuzzer. The main goal of Angora is to increase branch coverage by solving path constraints wi

### recon

- [projectdiscovery/nuclei](https://github.com/projectdiscovery/nuclei) - 33181 stars. Nuclei is a fast, customizable vulnerability scanner powered by the global security community and built on a simple YAML
- [projectdiscovery/katana](https://github.com/projectdiscovery/katana) - 17880 stars. A next-generation crawling and spidering framework. - projectdiscovery/katana
- [ffuf/ffuf](https://github.com/ffuf/ffuf) - 17096 stars. Fast web fuzzer written in Go. Contribute to ffuf/ffuf development by creating an account on GitHub.
- [owasp-amass/amass](https://github.com/owasp-amass/amass) - 15546 stars. In-depth attack surface mapping and asset discovery - owasp-amass/amass
- [maurosoria/dirsearch](https://github.com/maurosoria/dirsearch) - 15106 stars. Web path scanner. Contribute to maurosoria/dirsearch development by creating an account on GitHub.
- [projectdiscovery/subfinder](https://github.com/projectdiscovery/subfinder) - 14845 stars. Fast passive subdomain enumeration tool. Contribute to projectdiscovery/subfinder development by creating an account on 

### web

- [swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) - 82844 stars. A list of useful payloads and bypass for Web Application Security and Pentest/CTF - swisskyrepo/PayloadsAllTheThings
- [danielmiessler/SecLists](https://github.com/danielmiessler/SecLists) - 74877 stars. SecLists is the security tester's companion. It's a collection of multiple types of lists used during security assessmen
- [sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap) - 39010 stars. Automatic SQL injection and database takeover tool - sqlmapproject/sqlmap
- [s0md3v/XSStrike](https://github.com/s0md3v/XSStrike) - 15277 stars. Most advanced XSS scanner. Contribute to s0md3v/XSStrike development by creating an account on GitHub.
- [projectdiscovery/nuclei-templates](https://github.com/projectdiscovery/nuclei-templates) - 13383 stars. Community curated list of templates for the nuclei engine to find security vulnerabilities. - projectdiscovery/nuclei-te
- [chaitin/xray](https://github.com/chaitin/xray) - 11798 stars. 一款长亭自研的完善的安全评估工具，支持常见 web 安全问题扫描和自定义 poc \| 使用之前务必先阅读文档 - chaitin/xray

## GitHub atlas

2064 security repositories by topic. Top by stars:

- [trimstray/the-book-of-secret-knowledge](https://github.com/trimstray/the-book-of-secret-knowledge) - 260406 stars. A collection of inspiring lists, manuals, cheatsheets, blogs, hacks, one-liners, cli/web tools and more.
- [Hack-with-Github/Awesome-Hacking](https://github.com/Hack-with-Github/Awesome-Hacking) - 126201 stars. A collection of various awesome lists for hackers, pentesters and security researchers
- [sherlock-project/sherlock](https://github.com/sherlock-project/sherlock) - 115651 stars. Hunt down social media accounts by username across social networks
- [NationalSecurityAgency/ghidra](https://github.com/NationalSecurityAgency/ghidra) - 100936 stars. Ghidra is a software reverse engineering (SRE) framework
- [swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings) - 82852 stars. A list of useful payloads and bypass for Web Application Security and Pentest/CTF
- [Z4nzu/hackingtool](https://github.com/Z4nzu/hackingtool) - 82102 stars. ALL IN ONE Hacking Tool For Hackers

## Method index

- [HTTP Request Smuggling Research](https://portswigger.net/research/request-smuggling) - PortSwigger Research
- [Template Injection Research](https://portswigger.net/research/template-injection) - PortSwigger Research
- [Top 10 Web Hacking Techniques](https://portswigger.net/research/top-10-web-hacking-techniques) - PortSwigger Research
- [CSS:the bomb inside your inbox](https://portswigger.net/research/css-the-bomb-inside-your-inbox) - PortSwigger Research (68 code blocks)
- [CRLF-Powered Desync Attacks: Beheading HTTP Streams](https://portswigger.net/research/crlf-powered-desync-attacks) - PortSwigger Research (90 code blocks)
- [Can AI do novel security research? Meet the HTTP Terminator](https://portswigger.net/research/can-ai-do-novel-security-research) - PortSwigger Research (87 code blocks)
- [The Fragile Lock: Novel Bypasses For SAML Authentication](https://portswigger.net/research/the-fragile-lock) - PortSwigger Research (12 code blocks)
- [Achieving the impossible: XSS in innerHTML without equals](https://portswigger.net/research/achieving-the-impossible-xss-in-innerhtml-without-equals) - PortSwigger Research (75 code blocks)
- [Blind trust: stealing credentials through CSP](https://portswigger.net/research/blind-trust-stealing-credentials-through-csp) - PortSwigger Research (24 code blocks)
- [Making HTTP header injection critical with one prompt](https://portswigger.net/research/making-http-header-injection-critical-with-one-prompt) - PortSwigger Research (8 code blocks)
- [Probably Not Exploitable](https://projectdiscovery.io/blog/probably-not-exploitable) - ProjectDiscovery Blog
- [Introducing Neo Triage — ProjectDiscovery Blog](https://projectdiscovery.io/blog/introducing-neo-triage) - ProjectDiscovery Blog
- [Neo v1 is now available for everyone — ProjectDiscovery Blog](https://projectdiscovery.io/blog/neo-v1) - ProjectDiscovery Blog
- [Beyond the Model: Neo Hunts, Exploits, and Proves 22 Zero-Days.](https://projectdiscovery.io/blog/everyone-is-finding-vulns-the-hard-part-is-proving-them) - ProjectDiscovery Blog (63 code blocks)
- [Inside the benchmark: app architectures, walkthroughs of findings, and what each scanner actually caught](https://projectdiscovery.io/blog/inside-the-benchmark-pp-architectures-finding-walkthroughs-and-what-each-scanner-actually-caught) - ProjectDiscovery Blog (37 code blocks)
- [Nuclei Templates - April 2026](https://projectdiscovery.io/blog/nuclei-templates-april-2026) - ProjectDiscovery Blog (24 code blocks)
- [Open by design, trusted by enterprises: how we keep Nuclei templates reliable at scale](https://projectdiscovery.io/blog/open-by-design-trusted-by-enterprises-how-we-keep-nuclei-templates-reliable-at-scale) - ProjectDiscovery Blog
- [Nuclei Templates Monthly - May 2025](https://projectdiscovery.io/blog/nuclei-templates-monthly-may-2025) - ProjectDiscovery Blog (8 code blocks)
- [Hacking with AI: How to prevent your AI from going rogue](https://www.intigriti.com/researchers/blog/hacking-tools/hacking-with-ai-how-to-prevent-your-ai-from-going-rogue) - Intigriti
- [PHP2Shell revisited: What Intigriti’s insights tell us about real-world risk](https://www.intigriti.com/researchers/blog/news/php2shell-revisited-real-world-risk) - Intigriti (1 code blocks)
- [Intigriti Bug Bytes #241 - October 2026 🚀](https://www.intigriti.com/researchers/blog/bug-bytes/intigriti-bug-bytes-241-october-2026) - Intigriti (2 code blocks)
- [Exploiting LLM prompt injection vulnerabilities](https://www.intigriti.com/researchers/blog/hacking-tools/exploiting-advanced-llm-prompt-injection-vulnerabilities) - Intigriti (3 code blocks)
- [Understanding PHP2Shell: Everything you need to know about CVE-2026-109618 & CVE-2026-109619](https://www.intigriti.com/researchers/blog/news/php2shell) - Intigriti (11 code blocks)
- [The Exposure Debt Benchmark: What the 2026 Data Says About the Find-to-Fix Gap](https://www.hackerone.com/blog/exposure-management-find-to-fix-gap-2026) - HackerOne Blog
- [Make no mistakes: Blameless postmortems in an age of agents](https://www.hackerone.com/blog/make-no-mistakes-blameless-postmortems-in-an-age-of-agents) - HackerOne Blog
- [National Vulnerability Database Modernization in the Age of AI](https://www.hackerone.com/blog/nvd-modernization-vulnerability-prioritization) - HackerOne Blog
- [Your Releases Outpace Your Testing. It’s Time to Close the Gap.](https://www.hackerone.com/blog/continuous-security-validation-web-apps-apis) - HackerOne Blog
- [AI Security Has an Accountability Problem: What Our Latest Data Says About Who Owns the Call](https://www.hackerone.com/blog/ai-security-accountability) - HackerOne Blog
- [Measuring The Fix: How We Evaluate Frontier Models on Remediation Quality](https://www.hackerone.com/blog/measuring-the-fix-ctem-remediation-quality) - HackerOne Blog
- [Critical PHP Vulnerabilities: What Security Teams Need to Know](https://www.hackerone.com/blog/critical-php-vulnerabilities-hackerone-response) - HackerOne Blog
- [What’s New for HackerOne Researchers](https://www.hackerone.com/blog/whats-new-for-hackerone-researchers) - HackerOne Blog
- [How Model Evolution Has Reshaped Security AI Agents on H1 Platform](https://www.hackerone.com/blog/how-model-evolution-reshaped-ai-security-agents) - HackerOne Blog
- [Lost Book of the Forbidden Syscalls](https://0x00sec.org/deep-dive-series/) - 0x00sec
- [ZIP Parser Differentials and Mutable Encrypted Filenames - Part I](https://0x00sec.org/zip-parser-differentials-and-mutable-encrypted-filenames-part-i/) - 0x00sec (49 code blocks)
- [Lost Book of the Forbidden Syscalls](https://0x00sec.org/lbofs-debugging-and-anti-debugging-fun-with-ptrace-i/) - 0x00sec (122 code blocks)
- [reverse-engineering - 0x00sec](https://0x00sec.org/tag/reverse-engineering/) - 0x00sec
- [X86_64 Linux Remote Stack Overflow Exploitation — bypass PIE via format string information leak (Part 3)](https://0x00sec.org/x86_64-linux-remote-stack-overflow-exploitation-bypass-pie-via-format-string-information-leak-part-3/) - 0x00sec (31 code blocks)
- [X86_64 Linux Remote Stack Overflow Exploitation - ret2plt (part 2)](https://0x00sec.org/x86_64-linux-remote-stack-overflow-exploitation-ret2plt-part-2/) - 0x00sec (33 code blocks)
- [X86_64 Linux Remote Stack Overflow Exploitation -The Basic](https://0x00sec.org/x86_64-linux-remote-stack-overflow-exploitation-the-basic/) - 0x00sec
- [HackTricks](https://book.hacktricks.wiki/) - HackTricks (5 code blocks)

## Gaps and next passes

- Fewer than half of the threads have summaries. Run `extract.py` on the top URLs.
- Re-run monthly. Forum threads and tool stars move; disclosed reports age out of the first pages.
