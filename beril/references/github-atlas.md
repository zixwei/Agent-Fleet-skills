# GitHub security atlas

Snapshot: 2026-11-22. 2064 public repositories across 26 categories.

Built with `scripts/github_sweep.py` over topic and keyword searches, sorted by stars, minimum 15 stars, 30 per query. This is a high-signal slice, not every repository on GitHub.

Read the description and the last update before using a repo. Stars measure attention, not maintenance. Archived projects are marked.

## How to use the atlas

1. Pick the category that matches the target.
2. Filter by language and last update.
3. Open the README and the release page.
4. Prefer one maintained tool per job.
5. Add the tool to `data/re_tools.jsonl` only after a successful run.

## Category counts

| Category | Repos |
|---|---:|
| firmware | 117 |
| supply-chain | 114 |
| vuln-research | 112 |
| bug-bounty | 111 |
| pentest | 106 |
| ctf | 106 |
| red-team | 103 |
| web-security | 102 |
| detection | 102 |
| binary | 101 |
| web3 | 100 |
| blue-team | 100 |
| recon | 97 |
| mobile | 97 |
| cloud-security | 94 |
| toolkit | 89 |
| hardening | 89 |
| ghidra | 86 |
| ida-binja | 85 |
| wireless | 85 |
| ad | 83 |
| ai-security | 76 |
| crypto | 76 |
| data | 74 |
| fuzzing | 70 |
| burp | 68 |

## Top repositories overall

| Repo | Stars | Language | What it does |
|---|---:|---|---|
| [`trimstray/the-book-of-secret-knowledge`](https://github.com/trimstray/the-book-of-secret-knowledge) | 260406 | - | A collection of inspiring lists, manuals, cheatsheets, blogs, hacks, one-liners, cli/web tools and more. |
| [`Hack-with-Github/Awesome-Hacking`](https://github.com/Hack-with-Github/Awesome-Hacking) | 126201 | - | A collection of various awesome lists for hackers, pentesters and security researchers |
| [`sherlock-project/sherlock`](https://github.com/sherlock-project/sherlock) | 115651 | Python | Hunt down social media accounts by username across social networks |
| [`NationalSecurityAgency/ghidra`](https://github.com/NationalSecurityAgency/ghidra) | 100936 | Java | Ghidra is a software reverse engineering (SRE) framework |
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | Python | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`Z4nzu/hackingtool`](https://github.com/Z4nzu/hackingtool) | 82102 | Python | ALL IN ONE Hacking Tool For Hackers |
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | Python | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`danielmiessler/SecLists`](https://github.com/danielmiessler/SecLists) | 74880 | PHP | SecLists is the security tester's companion. It's a collection of multiple types of lists used during security assessments, collected in one place. List types include usernames, pa |
| [`WerWolv/ImHex`](https://github.com/WerWolv/ImHex) | 56000 | C++ | 🔍 A Hex Editor for Reverse Engineers, Programmers and people who value their retinas when working at 3 AM. |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | TypeScript | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities before they r |
| [`x64dbg/x64dbg`](https://github.com/x64dbg/x64dbg) | 50365 | C++ | An open-source user mode debugger for Windows. Optimized for reverse engineering and malware analysis. |
| [`mitmproxy/mitmproxy`](https://github.com/mitmproxy/mitmproxy) | 47135 | Python | An interactive TLS-capable intercepting HTTP proxy for penetration testers and software developers. |
| [`soxoj/maigret`](https://github.com/soxoj/maigret) | 41155 | Python | 🕵️‍♂️ Collect a dossier on a person by username from 6K websites |
| [`aquasecurity/trivy`](https://github.com/aquasecurity/trivy) | 39609 | Go | Find vulnerabilities, misconfigurations, secrets, SBOM in containers, Kubernetes, code repositories, clouds and more |
| [`sqlmapproject/sqlmap`](https://github.com/sqlmapproject/sqlmap) | 39013 | Python | Automatic SQL injection and database takeover tool |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | Python | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agentskills.io s |
| [`lissy93/web-check`](https://github.com/lissy93/web-check) | 35561 | TypeScript | 🕵️‍♂️ All-in-one OSINT tool for analysing any website |
| [`vxcontrol/pentagi`](https://github.com/vxcontrol/pentagi) | 34785 | Go | Fully autonomous AI Agents system capable of performing complex penetration testing tasks |
| [`TheAlgorithms/JavaScript`](https://github.com/TheAlgorithms/JavaScript) | 34269 | JavaScript | Algorithms and Data Structures implemented in JavaScript for beginners, following best practices. |
| [`OWASP/CheatSheetSeries`](https://github.com/OWASP/CheatSheetSeries) | 34155 | JavaScript | The OWASP Cheat Sheet Series was created to provide a concise collection of high value information on specific application security topics. |
| [`anoma/anoma`](https://github.com/anoma/anoma) | 33526 | Elixir | Reference implementation of Anoma |
| [`projectdiscovery/nuclei`](https://github.com/projectdiscovery/nuclei) | 33192 | Go | Nuclei is a fast, customizable vulnerability scanner powered by the global security community and built on a simple YAML-based DSL, enabling collaboration to tackle trending vulner |
| [`nginx/nginx`](https://github.com/nginx/nginx) | 32800 | C | The official NGINX Open Source repository. |
| [`imthenachoman/How-To-Secure-A-Linux-Server`](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server) | 32089 | - | An evolving how-to guide for securing a Linux server. |
| [`openssl/openssl`](https://github.com/openssl/openssl) | 31508 | C | General purpose TLS and crypto library |
| [`jivoi/awesome-osint`](https://github.com/jivoi/awesome-osint) | 31320 | - | :scream: A curated list of amazingly awesome OSINT |
| [`gitleaks/gitleaks`](https://github.com/gitleaks/gitleaks) | 31252 | Go | Find secrets with Gitleaks 🔑 |
| [`librepods-org/librepods`](https://github.com/librepods-org/librepods) | 30919 | Kotlin | AirPods liberated from Apple's ecosystem. |
| [`Infisical/infisical`](https://github.com/Infisical/infisical) | 30674 | TypeScript | Infisical is the open-source platform for securing secrets, certificates, access, and AI Agents. |
| [`The-Art-of-Hacking/h4cker`](https://github.com/The-Art-of-Hacking/h4cker) | 30308 | Jupyter Notebook | This repository is maintained by Omar Santos (@santosomar) and includes thousands of resources related to ethical hacking, bug bounties, digital forensics and incident response (DF |
| [`trufflesecurity/trufflehog`](https://github.com/trufflesecurity/trufflehog) | 29572 | Go | Find, verify, and analyze leaked credentials |
| [`OpenZeppelin/openzeppelin-contracts`](https://github.com/OpenZeppelin/openzeppelin-contracts) | 27363 | Solidity | OpenZeppelin Contracts is a library for secure smart contract development. |
| [`iBotPeaches/Apktool`](https://github.com/iBotPeaches/Apktool) | 27327 | Java | A tool for reverse engineering Android apk files |
| [`NVIDIA/SkillSpector`](https://github.com/NVIDIA/SkillSpector) | 27230 | Python | Security scanner for AI agent skills. Detect vulnerabilities, malicious patterns, security risks, prompt injection, data exfiltration, and supply-chain risks in Claude Code, Codex, |
| [`radareorg/radare2`](https://github.com/radareorg/radare2) | 25482 | C | UNIX-like reverse engineering framework and command-line toolset |
| [`arendst/Tasmota`](https://github.com/arendst/Tasmota) | 25009 | C | Alternative firmware for ESP8266 and ESP32 based devices with easy configuration using webUI, OTA updates, automation using timers or rules, expandability and entirely local contro |
| [`smicallef/spiderfoot`](https://github.com/smicallef/spiderfoot) | 24831 | Python | SpiderFoot automates OSINT for threat intelligence and mapping your attack surface. |
| [`qeeqbox/social-analyzer`](https://github.com/qeeqbox/social-analyzer) | 24601 | JavaScript | API, CLI, and Web App for analyzing and finding a person's profile in 1000 social media \ websites |
| [`chaitin/SafeLine`](https://github.com/chaitin/SafeLine) | 23386 | Go | SafeLine is a self-hosted WAF(Web Application Firewall) / reverse proxy to protect your web apps from attacks and exploits. |
| [`pranshuparmar/witr`](https://github.com/pranshuparmar/witr) | 23057 | Go | Why is this running? Trace any process, port, container, or file back to what started it - CLI + TUI.  |

## firmware

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`arendst/Tasmota`](https://github.com/arendst/Tasmota) | 25009 | active | Alternative firmware for ESP8266 and ESP32 based devices with easy configuration using webUI, OTA updates, automation using timers or rules, expandability and entirely lo |
| [`DarkFlippers/unleashed-firmware`](https://github.com/DarkFlippers/unleashed-firmware) | 22824 | active | Flipper Zero Unleashed Firmware |
| [`MarlinFirmware/Marlin`](https://github.com/MarlinFirmware/Marlin) | 17682 | active | Marlin is a firmware for RepRap 3D printers optimized for both 8 and 32 bit microcontrollers.  Marlin supports all common platforms.   Many commercial 3D printers come wi |
| [`flipperdevices/flipperzero-firmware`](https://github.com/flipperdevices/flipperzero-firmware) | 16955 | active | Flipper Zero firmware source code |
| [`ReFirmLabs/binwalk`](https://github.com/ReFirmLabs/binwalk) | 14733 | active | Firmware Analysis Tool |
| [`m3y54m/Embedded-Engineering-Roadmap`](https://github.com/m3y54m/Embedded-Engineering-Roadmap) | 13964 | active | Comprehensive roadmap for aspiring Embedded Systems Engineers, featuring a curated list of learning resources |
| [`justcallmekoko/ESP32Marauder`](https://github.com/justcallmekoko/ESP32Marauder) | 13193 | active | A suite of WiFi/Bluetooth offensive and defensive tools for the ESP32 |
| [`gurugio/lowlevelprogramming-university`](https://github.com/gurugio/lowlevelprogramming-university) | 12766 | archived | How to be low-level programmer |
| [`platformio/platformio-core`](https://github.com/platformio/platformio-core) | 9624 | active | Your Gateway to Embedded Software Development Excellence :alien: |
| [`Next-Flip/Momentum-Firmware`](https://github.com/Next-Flip/Momentum-Firmware) | 9422 | active | 🐬 Feature-rich, stable and customizable Flipper Firmware |
| [`crosspoint-reader/crosspoint-reader`](https://github.com/crosspoint-reader/crosspoint-reader) | 8918 | active | Open-source e-reader firmware |
| [`Ralim/IronOS`](https://github.com/Ralim/IronOS) | 8440 | active | Open Source Soldering Iron firmware |
| [`nodemcu/nodemcu-firmware`](https://github.com/nodemcu/nodemcu-firmware) | 7961 | active | Lua based interactive firmware for ESP8266, ESP8285 and ESP32 |
| [`Abdess/retrobios`](https://github.com/Abdess/retrobios) | 7751 | active | BIOS and firmware files for emulators, put in the right folder and checked: RetroArch, Batocera, Recalbox, EmuDeck, RetroBat, RetroDECK, Lakka, RetroPie, RomM, ROCKNIX, M |
| [`Asuswrt-Merlin/asuswrt-merlin.legacy`](https://github.com/Asuswrt-Merlin/asuswrt-merlin.legacy) | 6747 | archived | Enhanced version of Asus's router firmware (Asuswrt) (legacy code base) |
| [`tianocore/edk2`](https://github.com/tianocore/edk2) | 6435 | active | EDK II |
| [`Asuswrt-Merlin/asuswrt-merlin.ng`](https://github.com/Asuswrt-Merlin/asuswrt-merlin.ng) | 6294 | active | Third party firmware for Asus routers (main development) |
| [`armbian/build`](https://github.com/armbian/build) | 5551 | active | The official build framework for the Armbian Linux distribution. This repository contains the complete toolchain and scripts required to compile custom OS images from sou |
| [`timschneeb/GalaxyBudsClient`](https://github.com/timschneeb/GalaxyBudsClient) | 5388 | active | Unofficial Galaxy Buds Manager for Windows, macOS, Linux, and Android |
| [`fwupd/fwupd`](https://github.com/fwupd/fwupd) | 4236 | active | A system daemon to allow session software to update firmware |

## supply-chain

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`aquasecurity/trivy`](https://github.com/aquasecurity/trivy) | 39609 | active | Find vulnerabilities, misconfigurations, secrets, SBOM in containers, Kubernetes, code repositories, clouds and more |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`gitleaks/gitleaks`](https://github.com/gitleaks/gitleaks) | 31252 | active | Find secrets with Gitleaks 🔑 |
| [`Infisical/infisical`](https://github.com/Infisical/infisical) | 30674 | active | Infisical is the open-source platform for securing secrets, certificates, access, and AI Agents. |
| [`trufflesecurity/trufflehog`](https://github.com/trufflesecurity/trufflehog) | 29572 | active | Find, verify, and analyze leaked credentials |
| [`NVIDIA/SkillSpector`](https://github.com/NVIDIA/SkillSpector) | 27230 | active | Security scanner for AI agent skills. Detect vulnerabilities, malicious patterns, security risks, prompt injection, data exfiltration, and supply-chain risks in Claude Co |
| [`MobSF/Mobile-Security-Framework-MobSF`](https://github.com/MobSF/Mobile-Security-Framework-MobSF) | 22292 | active | Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework cap |
| [`prowler-cloud/prowler`](https://github.com/prowler-cloud/prowler) | 15301 | active | This repo hosts Prowler OSS (CLI and Local Server). Prowler is the world’s most widely used open-source cloud security platform that automates security and compliance acr |
| [`bytebase/bytebase`](https://github.com/bytebase/bytebase) | 14726 | active | Database governance built for humans and agents - controlling changes and access across every major database. |
| [`openai/codex-security`](https://github.com/openai/codex-security) | 14264 | active | OpenAI's Codex Security CLI and TypeScript SDK for finding, validating, and fixing security vulnerabilities. npm: https://www.npmjs.com/package/@openai/codex-security |
| [`gravitl/netmaker`](https://github.com/gravitl/netmaker) | 11882 | active | Netmaker makes networks with WireGuard. Netmaker automates fast, secure, and distributed virtual networks. |
| [`bunkerity/bunkerweb`](https://github.com/bunkerity/bunkerweb) | 11302 | active | 🛡️ Open-source and cloud-native Web Application Firewall (WAF) |
| [`anchore/syft`](https://github.com/anchore/syft) | 9946 | active | CLI tool and library for generating a Software Bill of Materials from container images and filesystems |
| [`xonsh/xonsh`](https://github.com/xonsh/xonsh) | 9704 | active | 🐚 Python-powered shell. Full-featured, cross-platform and AI-friendly. |
| [`We5ter/Scanners-Box`](https://github.com/We5ter/Scanners-Box) | 9224 | active | The Ultimate Open-Source Security Arsenal for Hackers, Enterprises, and AI Agents--面向极客、企业与 AI 智能体的全域开源网络安全工具矩阵 |
| [`firezone/firezone`](https://github.com/firezone/firezone) | 9197 | active | Blazing-fast remote access |
| [`turbot/steampipe`](https://github.com/turbot/steampipe) | 8019 | active | Zero-ETL, infinite possibilities. Live query APIs, code & more with SQL. No DB required. |
| [`lintsinghua/DeepAudit`](https://github.com/lintsinghua/DeepAudit) | 7341 | active | DeepAudit：人人拥有的 AI 黑客战队，让漏洞挖掘触手可及。小白一键部署运行，自主协作审计 + 自动化沙箱 PoC 验证。支持 Ollama 私有部署 ，一键生成报告。支持中转站。​让安全不再昂贵，让审计不再复杂。 |
| [`aquasecurity/tfsec`](https://github.com/aquasecurity/tfsec) | 7058 | active | Tfsec is now part of Trivy |

## vuln-research

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`projectdiscovery/nuclei`](https://github.com/projectdiscovery/nuclei) | 33192 | active | Nuclei is a fast, customizable vulnerability scanner powered by the global security community and built on a simple YAML-based DSL, enabling collaboration to tackle trend |
| [`The-Art-of-Hacking/h4cker`](https://github.com/The-Art-of-Hacking/h4cker) | 30308 | active | This repository is maintained by Omar Santos (@santosomar) and includes thousands of resources related to ethical hacking, bug bounties, digital forensics and incident re |
| [`chaitin/SafeLine`](https://github.com/chaitin/SafeLine) | 23386 | active | SafeLine is a self-hosted WAF(Web Application Firewall) / reverse proxy to protect your web apps from attacks and exploits. |
| [`vitalysim/Awesome-Hacking-Resources`](https://github.com/vitalysim/Awesome-Hacking-Resources) | 17664 | active | A collection of hacking / penetration testing resources to make you better! |
| [`Gallopsled/pwntools`](https://github.com/Gallopsled/pwntools) | 13883 | active | CTF framework and exploit development library |
| [`future-architect/vuls`](https://github.com/future-architect/vuls) | 12363 | active | Agent-less vulnerability scanner for Linux, FreeBSD, Container, WordPress, Programming language libraries, Network devices |
| [`chaitin/xray`](https://github.com/chaitin/xray) | 11799 | active | 一款长亭自研的完善的安全评估工具，支持常见 web 安全问题扫描和自定义 poc / 使用之前务必先阅读文档 |
| [`google/osv-scanner`](https://github.com/google/osv-scanner) | 11593 | active | Vulnerability scanner written in Go which uses the data provided by https://osv.dev |
| [`edoardottt/awesome-hacker-search-engines`](https://github.com/edoardottt/awesome-hacker-search-engines) | 11542 | active | A curated list of awesome search engines useful during Penetration testing, Vulnerability assessments, Red/Blue Team operations, Bug Bounty and more |
| [`friuns2/BlackFriday-GPTs-Prompts`](https://github.com/friuns2/BlackFriday-GPTs-Prompts) | 9908 | active | List of free GPTs that doesn't require plus subscription  |
| [`frohoff/ysoserial`](https://github.com/frohoff/ysoserial) | 9106 | active | A proof-of-concept tool for generating payloads that exploit unsafe Java object deserialization. |
| [`SecWiki/windows-kernel-exploits`](https://github.com/SecWiki/windows-kernel-exploits) | 8770 | active | windows-kernel-exploits   Windows平台提权漏洞集合 |
| [`hugsy/gef`](https://github.com/hugsy/gef) | 8445 | active | GEF (GDB Enhanced Features) - a modern experience for GDB with advanced debugging capabilities for exploit devs & reverse engineers on Linux |
| [`trickest/cve`](https://github.com/trickest/cve) | 8239 | active | Gather and update all available and newest CVEs with their PoC. |
| [`nomi-sec/PoC-in-GitHub`](https://github.com/nomi-sec/PoC-in-GitHub) | 8221 | active | 📡 PoC auto collect from GitHub. ⚠️ Be careful Malware. |
| [`yaklang/yakit`](https://github.com/yaklang/yakit) | 7907 | active | Cyber Security ALL-IN-ONE Platform |
| [`Mr-xn/Penetration_Testing_POC`](https://github.com/Mr-xn/Penetration_Testing_POC) | 7586 | active | 渗透测试有关的POC、EXP、脚本、提权、小工具等---About penetration-testing python-script poc getshell csrf xss cms php-getshell domainmod-xss csrf-webshell cobub-razor cve rce sql sql-poc poc |
| [`lintsinghua/DeepAudit`](https://github.com/lintsinghua/DeepAudit) | 7341 | active | DeepAudit：人人拥有的 AI 黑客战队，让漏洞挖掘触手可及。小白一键部署运行，自主协作审计 + 自动化沙箱 PoC 验证。支持 Ollama 私有部署 ，一键生成报告。支持中转站。​让安全不再昂贵，让审计不再复杂。 |
| [`liamg/traitor`](https://github.com/liamg/traitor) | 7157 | active | :arrow_up: :skull_and_crossbones: :fire: Automatic Linux privesc via exploitation of low-hanging fruit e.g. gtfobins, pwnkit, dirty pipe, +w docker.sock |
| [`infobyte/faraday`](https://github.com/infobyte/faraday) | 6877 | active | Open-source and AI-powered cybersecurity tools for offensive security, vulnerability management, and autonomous pentesting. Built by hackers in Latin America, used worldw |

## bug-bounty

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`Hack-with-Github/Awesome-Hacking`](https://github.com/Hack-with-Github/Awesome-Hacking) | 126201 | active | A collection of various awesome lists for hackers, pentesters and security researchers |
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | active | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`elementalsouls/Claude-BugHunter`](https://github.com/elementalsouls/Claude-BugHunter) | 16733 | active | A Claude Code skill bundle for bug hunting and external red-team work - 82 skills, 15 slash commands, 681 disclosed-report patterns curated across 24 core vulnerability c |
| [`BaaRai/ARTEX`](https://github.com/BaaRai/ARTEX) | 15573 | active | ARTEX - AI autonomous penetration testing system (English translation; upstream: Autumn-27/ARTEX, AGPL-3.0) |
| [`maurosoria/dirsearch`](https://github.com/maurosoria/dirsearch) | 15108 | active | Web path scanner |
| [`projectdiscovery/subfinder`](https://github.com/projectdiscovery/subfinder) | 14845 | active | Fast passive subdomain enumeration tool. |
| [`0x4m4/hexstrike-ai`](https://github.com/0x4m4/hexstrike-ai) | 13812 | active | HexStrike AI MCP Agents is an advanced MCP server that lets AI agents (Claude, GPT, Copilot, etc.) autonomously run 150+ cybersecurity tools for automated pentesting, vul |
| [`projectdiscovery/nuclei-templates`](https://github.com/projectdiscovery/nuclei-templates) | 13384 | active | Community curated list of templates for the nuclei engine to find security vulnerabilities. |
| [`dstotijn/hetty`](https://github.com/dstotijn/hetty) | 12553 | active | An HTTP toolkit for security research. |
| [`nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters`](https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters) | 12359 | active | A list of resources for those interested in getting started in bug bounties |
| [`edoardottt/awesome-hacker-search-engines`](https://github.com/edoardottt/awesome-hacker-search-engines) | 11542 | active | A curated list of awesome search engines useful during Penetration testing, Vulnerability assessments, Red/Blue Team operations, Bug Bounty and more |
| [`blacklanternsecurity/bbot`](https://github.com/blacklanternsecurity/bbot) | 11176 | active | The recursive internet scanner for hackers. 🧡 |
| [`projectdiscovery/httpx`](https://github.com/projectdiscovery/httpx) | 10641 | active | httpx is a fast and multi-purpose HTTP toolkit that allows running multiple probes using the retryablehttp library. |
| [`OWASP/wstg`](https://github.com/OWASP/wstg) | 10373 | active | The Web Security Testing Guide is a comprehensive Open Source guide to testing the security of web applications and web services. |
| [`shmilylty/OneForAll`](https://github.com/shmilylty/OneForAll) | 10158 | active | OneForAll是一款功能强大的子域收集工具 |
| [`yogeshojha/rengine`](https://github.com/yogeshojha/rengine) | 8956 | active | reNgine is an automated reconnaissance framework for web applications with a focus on highly configurable streamlined recon process via Engines, recon data correlation an |
| [`six2dez/reconftw`](https://github.com/six2dez/reconftw) | 8402 | active | reconFTW is a tool designed to perform automated recon on a target domain by running the best set of tools to perform scanning and finding out vulnerabilities |
| [`KathanP19/HowToHunt`](https://github.com/KathanP19/HowToHunt) | 7395 | active | Collection of methodology and test case for various web vulnerabilities. |
| [`LasCC/HackTools`](https://github.com/LasCC/HackTools) | 7127 | active | The all-in-one browser extension for offensive security professionals 🛠 |

## pentest

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`Hack-with-Github/Awesome-Hacking`](https://github.com/Hack-with-Github/Awesome-Hacking) | 126201 | active | A collection of various awesome lists for hackers, pentesters and security researchers |
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | active | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`vxcontrol/pentagi`](https://github.com/vxcontrol/pentagi) | 34785 | active | Fully autonomous AI Agents system capable of performing complex penetration testing tasks |
| [`The-Art-of-Hacking/h4cker`](https://github.com/The-Art-of-Hacking/h4cker) | 30308 | active | This repository is maintained by Omar Santos (@santosomar) and includes thousands of resources related to ethical hacking, bug bounties, digital forensics and incident re |
| [`qeeqbox/social-analyzer`](https://github.com/qeeqbox/social-analyzer) | 24601 | active | API, CLI, and Web App for analyzing and finding a person's profile in 1000 social media \ websites |
| [`vitalysim/Awesome-Hacking-Resources`](https://github.com/vitalysim/Awesome-Hacking-Resources) | 17664 | active | A collection of hacking / penetration testing resources to make you better! |
| [`GreyDGL/PentestGPT`](https://github.com/GreyDGL/PentestGPT) | 16549 | active | Automated Penetration Testing Agentic Framework Powered by Large Language Models |
| [`Datalux/Osintgram`](https://github.com/Datalux/Osintgram) | 15734 | active | Osintgram is a OSINT tool on Instagram. It offers an interactive shell to perform analysis on Instagram account of any users by its nickname |
| [`sundowndev/hacker-roadmap`](https://github.com/sundowndev/hacker-roadmap) | 15721 | archived | A collection of hacking tools, resources and references to practice ethical hacking. |
| [`BaaRai/ARTEX`](https://github.com/BaaRai/ARTEX) | 15573 | active | ARTEX - AI autonomous penetration testing system (English translation; upstream: Autumn-27/ARTEX, AGPL-3.0) |
| [`maurosoria/dirsearch`](https://github.com/maurosoria/dirsearch) | 15108 | active | Web path scanner |
| [`qazbnm456/awesome-web-security`](https://github.com/qazbnm456/awesome-web-security) | 14085 | active | 🐶 A curated list of Web Security materials and resources. |
| [`0x4m4/hexstrike-ai`](https://github.com/0x4m4/hexstrike-ai) | 13812 | active | HexStrike AI MCP Agents is an advanced MCP server that lets AI agents (Claude, GPT, Copilot, etc.) autonomously run 150+ cybersecurity tools for automated pentesting, vul |
| [`vanhauser-thc/thc-hydra`](https://github.com/vanhauser-thc/thc-hydra) | 12560 | active | hydra |
| [`nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters`](https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters) | 12359 | active | A list of resources for those interested in getting started in bug bounties |
| [`horsicq/Detect-It-Easy`](https://github.com/horsicq/Detect-It-Easy) | 11991 | active | Program for determining types of files for Windows, Linux and MacOS. |
| [`1N3/Sn1per`](https://github.com/1N3/Sn1per) | 11564 | active | Automated penetration testing & attack surface management platform. Recon, scan, exploit, report - 600+ exploits, 90+ integrations, 10K+ detections. |

## ctf

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`Z4nzu/hackingtool`](https://github.com/Z4nzu/hackingtool) | 82102 | active | ALL IN ONE Hacking Tool For Hackers |
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | active | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`x64dbg/x64dbg`](https://github.com/x64dbg/x64dbg) | 50365 | active | An open-source user mode debugger for Windows. Optimized for reverse engineering and malware analysis. |
| [`bee-san/Ciphey`](https://github.com/bee-san/Ciphey) | 21782 | active | ⚡ Automatically decrypt encryptions without knowing the key or cipher, decode encodings, and crack hashes ⚡ |
| [`vitalysim/Awesome-Hacking-Resources`](https://github.com/vitalysim/Awesome-Hacking-Resources) | 17664 | active | A collection of hacking / penetration testing resources to make you better! |
| [`juice-shop/juice-shop`](https://github.com/juice-shop/juice-shop) | 14510 | active | OWASP Juice Shop: Probably the most modern and sophisticated insecure web application |
| [`Gallopsled/pwntools`](https://github.com/Gallopsled/pwntools) | 13883 | active | CTF framework and exploit development library |
| [`0x4m4/hexstrike-ai`](https://github.com/0x4m4/hexstrike-ai) | 13812 | active | HexStrike AI MCP Agents is an advanced MCP server that lets AI agents (Claude, GPT, Copilot, etc.) autonomously run 150+ cybersecurity tools for automated pentesting, vul |
| [`apsdehal/awesome-ctf`](https://github.com/apsdehal/awesome-ctf) | 12013 | active | A curated list of CTF frameworks, libraries, resources and softwares |
| [`pwndbg/pwndbg`](https://github.com/pwndbg/pwndbg) | 11238 | active | Exploit Development and Reverse Engineering with GDB & LLDB Made Easy |
| [`ctf-wiki/ctf-wiki`](https://github.com/ctf-wiki/ctf-wiki) | 9783 | active | Come and join us, we need you! |
| [`AIPentest/CyberStrikeAI`](https://github.com/AIPentest/CyberStrikeAI) | 8922 | active | The system of action for AI-native cybersecurity-where intent becomes governed execution, evidence becomes operational memory, and every operation improves the next. |
| [`hugsy/gef`](https://github.com/hugsy/gef) | 8445 | active | GEF (GDB Enhanced Features) - a modern experience for GDB with advanced debugging capabilities for exploit devs & reverse engineers on Linux |
| [`CTFd/CTFd`](https://github.com/CTFd/CTFd) | 6920 | active | CTFs as you need them |
| [`mishakorzik/AllHackingTools`](https://github.com/mishakorzik/AllHackingTools) | 6555 | active |  All-in-One Hacking Tools For Hackers! And more hacking tools! For termux. |
| [`facebookarchive/fbctf`](https://github.com/facebookarchive/fbctf) | 6535 | archived | Platform to host Capture the Flag competitions |
| [`DominicBreuker/pspy`](https://github.com/DominicBreuker/pspy) | 6254 | active | Monitor linux processes without root permissions |
| [`RPISEC/MBE`](https://github.com/RPISEC/MBE) | 6108 | active | Course materials for Modern Binary Exploitation by RPISEC |
| [`alpkeskin/mosint`](https://github.com/alpkeskin/mosint) | 6087 | active | An automated e-mail OSINT tool |
| [`ffffffff0x/1earn`](https://github.com/ffffffff0x/1earn) | 5738 | active | ffffffff0x 团队维护的安全知识框架,内容包括不仅限于 web安全、工控安全、取证、应急、蓝队设施部署、后渗透、Linux安全、各类靶机writup |

## red-team

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`sherlock-project/sherlock`](https://github.com/sherlock-project/sherlock) | 115651 | active | Hunt down social media accounts by username across social networks |
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | active | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`x64dbg/x64dbg`](https://github.com/x64dbg/x64dbg) | 50365 | active | An open-source user mode debugger for Windows. Optimized for reverse engineering and malware analysis. |
| [`soxoj/maigret`](https://github.com/soxoj/maigret) | 41155 | active | 🕵️‍♂️ Collect a dossier on a person by username from 6K websites |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`vxcontrol/pentagi`](https://github.com/vxcontrol/pentagi) | 34785 | active | Fully autonomous AI Agents system capable of performing complex penetration testing tasks |
| [`bettercap/bettercap`](https://github.com/bettercap/bettercap) | 20338 | active | The Swiss Army knife for 802.11, BLE, HID, CAN-bus, IPv4 and IPv6 networks reconnaissance and MITM attacks. |
| [`laramies/theHarvester`](https://github.com/laramies/theHarvester) | 18382 | active | E-mails, subdomains and names Harvester - OSINT  |
| [`elementalsouls/Claude-BugHunter`](https://github.com/elementalsouls/Claude-BugHunter) | 16733 | active | A Claude Code skill bundle for bug hunting and external red-team work - 82 skills, 15 slash commands, 681 disclosed-report patterns curated across 24 core vulnerability c |
| [`BaaRai/ARTEX`](https://github.com/BaaRai/ARTEX) | 15573 | active | ARTEX - AI autonomous penetration testing system (English translation; upstream: Autumn-27/ARTEX, AGPL-3.0) |
| [`maurosoria/dirsearch`](https://github.com/maurosoria/dirsearch) | 15108 | active | Web path scanner |
| [`GTFOBins/GTFOBins.github.io`](https://github.com/GTFOBins/GTFOBins.github.io) | 13888 | active | GTFOBins is a curated list of Unix-like executables that can be used to bypass local security restrictions in misconfigured systems. |
| [`BishopFox/sliver`](https://github.com/BishopFox/sliver) | 12394 | active | Adversary Emulation Framework |
| [`edoardottt/awesome-hacker-search-engines`](https://github.com/edoardottt/awesome-hacker-search-engines) | 11542 | active | A curated list of awesome search engines useful during Penetration testing, Vulnerability assessments, Red/Blue Team operations, Bug Bounty and more |
| [`SnailSploit/Claude-Red`](https://github.com/SnailSploit/Claude-Red) | 11364 | active | claude-red is a curated library of offensive security skills designed for the Claude skills system. Each skill is a structured SKILL.md file that primes Claude with exper |
| [`infosecn1nja/Red-Teaming-Toolkit`](https://github.com/infosecn1nja/Red-Teaming-Toolkit) | 10882 | active | This repository contains cutting-edge open-source security tools (OST) for a red teamer and threat hunter. |
| [`A-poc/RedTeam-Tools`](https://github.com/A-poc/RedTeam-Tools) | 10348 | active | Tools and Techniques for Red Team / Penetration Testing |
| [`samratashok/nishang`](https://github.com/samratashok/nishang) | 10216 | active | Nishang - Offensive PowerShell for red team, penetration testing and offensive security.  |

## web-security

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`sqlmapproject/sqlmap`](https://github.com/sqlmapproject/sqlmap) | 39013 | active | Automatic SQL injection and database takeover tool |
| [`OWASP/CheatSheetSeries`](https://github.com/OWASP/CheatSheetSeries) | 34155 | active | The OWASP Cheat Sheet Series was created to provide a concise collection of high value information on specific application security topics. |
| [`chaitin/SafeLine`](https://github.com/chaitin/SafeLine) | 23386 | active | SafeLine is a self-hosted WAF(Web Application Firewall) / reverse proxy to protect your web apps from attacks and exploits. |
| [`MobSF/Mobile-Security-Framework-MobSF`](https://github.com/MobSF/Mobile-Security-Framework-MobSF) | 22292 | active | Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework cap |
| [`elementalsouls/Claude-BugHunter`](https://github.com/elementalsouls/Claude-BugHunter) | 16733 | active | A Claude Code skill bundle for bug hunting and external red-team work - 82 skills, 15 slash commands, 681 disclosed-report patterns curated across 24 core vulnerability c |
| [`crowdsecurity/crowdsec`](https://github.com/crowdsecurity/crowdsec) | 15747 | active | Open-source IDS/IPS, WAF and bot detection for Linux, Windows, Docker and Kubernetes, with a crowdsourced blocklist of malicious IPs. |
| [`Hacker0x01/hacker101`](https://github.com/Hacker0x01/hacker101) | 14651 | active | Source code for Hacker101.com - a free online web and mobile security class. |
| [`juice-shop/juice-shop`](https://github.com/juice-shop/juice-shop) | 14510 | active | OWASP Juice Shop: Probably the most modern and sophisticated insecure web application |
| [`openai/codex-security`](https://github.com/openai/codex-security) | 14264 | active | OpenAI's Codex Security CLI and TypeScript SDK for finding, validating, and fixing security vulnerabilities. npm: https://www.npmjs.com/package/@openai/codex-security |
| [`digininja/DVWA`](https://github.com/digininja/DVWA) | 14065 | active | Damn Vulnerable Web Application (DVWA) |
| [`nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters`](https://github.com/nahamsec/Resources-for-Beginner-Bug-Bounty-Hunters) | 12359 | active | A list of resources for those interested in getting started in bug bounties |
| [`bunkerity/bunkerweb`](https://github.com/bunkerity/bunkerweb) | 11302 | active | 🛡️ Open-source and cloud-native Web Application Firewall (WAF) |
| [`frappe/frappe`](https://github.com/frappe/frappe) | 11140 | active | Low code web framework for real world applications, in Python and Javascript |
| [`OWASP/wstg`](https://github.com/OWASP/wstg) | 10373 | active | The Web Security Testing Guide is a comprehensive Open Source guide to testing the security of web applications and web services. |
| [`owasp-modsecurity/ModSecurity`](https://github.com/owasp-modsecurity/ModSecurity) | 9885 | active | ModSecurity is an open source, cross platform web application firewall (WAF) engine for Apache, IIS and Nginx. It has a robust event-based programming language which prov |
| [`0xInfection/Awesome-WAF`](https://github.com/0xInfection/Awesome-WAF) | 7688 | active | Everything about Web Application Firewalls (WAFs) from Security Standpoint! 🔥 |
| [`infoslack/awesome-web-hacking`](https://github.com/infoslack/awesome-web-hacking) | 7366 | active | A list of web application security |
| [`paragonie/awesome-appsec`](https://github.com/paragonie/awesome-appsec) | 7152 | active | A curated list of resources for learning about application security |

## detection

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`pranshuparmar/witr`](https://github.com/pranshuparmar/witr) | 23057 | active | Why is this running? Trace any process, port, container, or file back to what started it - CLI + TUI.  |
| [`wazuh/wazuh`](https://github.com/wazuh/wazuh) | 17858 | active | Wazuh - The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads. |
| [`moonD4rk/HackBrowserData`](https://github.com/moonD4rk/HackBrowserData) | 14735 | active | Decrypt and export passwords, cookies, history and more from 20 browsers on Windows, macOS and Linux. For authorized security research, incident response and DFIR. |
| [`dastergon/awesome-sre`](https://github.com/dastergon/awesome-sre) | 13794 | active | A curated list of Site Reliability and Production Engineering resources. |
| [`kubeshark/kubeshark`](https://github.com/kubeshark/kubeshark) | 12143 | active | eBPF-powered network observability for Kubernetes. Indexes L4/L7 traffic with full K8s context, decrypts TLS without keys. Queryable by AI agents via MCP and humans via d |
| [`upgundecha/howtheysre`](https://github.com/upgundecha/howtheysre) | 9850 | active | A curated collection of publicly available resources on how technology and tech-savvy organizations around the world practice Site Reliability Engineering (SRE) |
| [`meirwah/awesome-incident-response`](https://github.com/meirwah/awesome-incident-response) | 9594 | active | A curated list of tools for incident response |
| [`toniblyx/my-arsenal-of-aws-security-tools`](https://github.com/toniblyx/my-arsenal-of-aws-security-tools) | 9539 | active | List of open source tools for AWS security: defensive, offensive, auditing, DFIR, etc. |
| [`OneUptime/oneuptime`](https://github.com/OneUptime/oneuptime) | 7834 | active | Complete open-source monitoring and observability platform. |
| [`beenuar/AiSOC`](https://github.com/beenuar/AiSOC) | 7335 | active | Open-source AI Security Operations Center: alert fusion, LLM-agent triage, MITRE ATT&CK investigation, and a replayable decision ledger for every agent step. Self-hostabl |
| [`OISF/suricata`](https://github.com/OISF/suricata) | 6921 | active | Suricata is a network Intrusion Detection System, Intrusion Prevention System and Network Security Monitoring engine developed by the OISF and the Suricata community. |
| [`MISP/MISP`](https://github.com/MISP/MISP) | 6691 | active | MISP (core software) - Open Source Threat Intelligence and Sharing Platform |
| [`elceef/dnstwist`](https://github.com/elceef/dnstwist) | 5784 | active | Domain name permutation engine for detecting homograph phishing attacks, typo squatting, and brand impersonation |
| [`SwiftOnSecurity/sysmon-config`](https://github.com/SwiftOnSecurity/sysmon-config) | 5736 | active | Sysmon configuration file template with default high-quality event tracing |
| [`cugu/awesome-forensics`](https://github.com/cugu/awesome-forensics) | 5258 | active | ⭐️ A curated list of awesome forensic analysis tools and resources |
| [`Security-Onion-Solutions/securityonion`](https://github.com/Security-Onion-Solutions/securityonion) | 4997 | active | Security Onion is a free and open platform for threat hunting, enterprise security monitoring, and log management. It includes our own interfaces for alerting, dashboards |
| [`A-poc/BlueTeam-Tools`](https://github.com/A-poc/BlueTeam-Tools) | 4929 | active | Tools and Techniques for Blue Team / Incident Response |
| [`intelowlproject/IntelOwl`](https://github.com/intelowlproject/IntelOwl) | 4826 | active | IntelOwl: manage your Threat Intelligence at scale |
| [`0x4D31/awesome-threat-detection`](https://github.com/0x4D31/awesome-threat-detection) | 4806 | active | ✨ A curated list of awesome threat detection and hunting resources 🕵️‍♂️ |

## binary

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`Hack-with-Github/Awesome-Hacking`](https://github.com/Hack-with-Github/Awesome-Hacking) | 126201 | active | A collection of various awesome lists for hackers, pentesters and security researchers |
| [`NationalSecurityAgency/ghidra`](https://github.com/NationalSecurityAgency/ghidra) | 100936 | active | Ghidra is a software reverse engineering (SRE) framework |
| [`WerWolv/ImHex`](https://github.com/WerWolv/ImHex) | 56000 | active | 🔍 A Hex Editor for Reverse Engineers, Programmers and people who value their retinas when working at 3 AM. |
| [`x64dbg/x64dbg`](https://github.com/x64dbg/x64dbg) | 50365 | active | An open-source user mode debugger for Windows. Optimized for reverse engineering and malware analysis. |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`librepods-org/librepods`](https://github.com/librepods-org/librepods) | 30919 | active | AirPods liberated from Apple's ecosystem. |
| [`The-Art-of-Hacking/h4cker`](https://github.com/The-Art-of-Hacking/h4cker) | 30308 | active | This repository is maintained by Omar Santos (@santosomar) and includes thousands of resources related to ethical hacking, bug bounties, digital forensics and incident re |
| [`iBotPeaches/Apktool`](https://github.com/iBotPeaches/Apktool) | 27327 | active | A tool for reverse engineering Android apk files |
| [`radareorg/radare2`](https://github.com/radareorg/radare2) | 25482 | active | UNIX-like reverse engineering framework and command-line toolset |
| [`MobSF/Mobile-Security-Framework-MobSF`](https://github.com/MobSF/Mobile-Security-Framework-MobSF) | 22292 | active | Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework cap |
| [`rizinorg/cutter`](https://github.com/rizinorg/cutter) | 20249 | active | Free and Open Source Reverse Engineering Platform powered by rizin |
| [`vitalysim/Awesome-Hacking-Resources`](https://github.com/vitalysim/Awesome-Hacking-Resources) | 17664 | active | A collection of hacking / penetration testing resources to make you better! |
| [`Tyrrrz/YoutubeDownloader`](https://github.com/Tyrrrz/YoutubeDownloader) | 16781 | active | Downloads videos and playlists from YouTube |
| [`mytechnotalent/Reverse-Engineering`](https://github.com/mytechnotalent/Reverse-Engineering) | 15232 | active | A FREE comprehensive reverse engineering tutorial covering x86, x64, 32-bit/64-bit ARM, 8-bit AVR and 32-bit RISC-V architectures. |
| [`rshipp/awesome-malware-analysis`](https://github.com/rshipp/awesome-malware-analysis) | 14453 | active | Defund the Police. |
| [`HIllya51/LunaTranslator`](https://github.com/HIllya51/LunaTranslator) | 14261 | active | 视觉小说翻译器 / Visual Novel Translator |
| [`seemoo-lab/openhaystack`](https://github.com/seemoo-lab/openhaystack) | 14247 | active | Build your own 'AirTags' 🏷 today! Framework for tracking personal Bluetooth devices via Apple's massive Find My network. |
| [`ytisf/theZoo`](https://github.com/ytisf/theZoo) | 13596 | active | A repository of LIVE malwares for your own joy and pleasure. theZoo is a project created to make the possibility of malware analysis open and available to the public. |
| [`mrexodia/ida-pro-mcp`](https://github.com/mrexodia/ida-pro-mcp) | 13542 | active | AI-powered reverse engineering assistant that bridges IDA Pro with language models through MCP. |
| [`projectdiscovery/nuclei-templates`](https://github.com/projectdiscovery/nuclei-templates) | 13384 | active | Community curated list of templates for the nuclei engine to find security vulnerabilities. |

## web3

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`OpenZeppelin/openzeppelin-contracts`](https://github.com/OpenZeppelin/openzeppelin-contracts) | 27363 | active | OpenZeppelin Contracts is a library for secure smart contract development. |
| [`ConsenSysDiligence/smart-contract-best-practices`](https://github.com/ConsenSysDiligence/smart-contract-best-practices) | 7562 | active | A guide to smart contract security best practices |
| [`ConsenSysDiligence/mythril`](https://github.com/ConsenSysDiligence/mythril) | 4278 | active | Mythril is a symbolic-execution-based securty analysis tool for EVM bytecode. It detects security vulnerabilities in smart contracts built for Ethereum and other EVM-comp |
| [`fravoll/solidity-patterns`](https://github.com/fravoll/solidity-patterns) | 3325 | active | A compilation of patterns and best practices for the smart contract programming language Solidity |
| [`crytic/echidna`](https://github.com/crytic/echidna) | 3201 | active | Ethereum smart contract fuzzer |
| [`FISCO-BCOS/FISCO-BCOS`](https://github.com/FISCO-BCOS/FISCO-BCOS) | 2620 | active | FISCO BCOS（发音为/ˈfɪskl bi:ˈkɒz/）是一个稳定、高效、安全的许可区块链平台，已被广泛应用于现实的行业应用。截至目前，已拥有5000多家企事业单位，400多个产业数字化标杆应用，涵盖文化版权、司法服务、政府服务、物联网、金融、智慧社区、房地产建设、社区治理、乡村振兴等领域。FISCO BCOS (pronounce |
| [`kadenzipfel/smart-contract-vulnerabilities`](https://github.com/kadenzipfel/smart-contract-vulnerabilities) | 2500 | active | A collection of smart contract vulnerabilities along with prevention methods |
| [`crytic/not-so-smart-contracts`](https://github.com/crytic/not-so-smart-contracts) | 2224 | archived | Examples of Solidity security issues |
| [`transmissions11/solcurity`](https://github.com/transmissions11/solcurity) | 2183 | active | Opinionated security and code quality standard for Solidity smart contracts. |
| [`Cyfrin/security-and-auditing-full-course-s23`](https://github.com/Cyfrin/security-and-auditing-full-course-s23) | 1973 | active | The ultimate, most advanced, security, DeFi, assembly, web3 auditor course ever created.  |
| [`x676f64/secureum-mind_map`](https://github.com/x676f64/secureum-mind_map) | 1898 | active | Central Repository for the Epoch 0 coursework and quizzes. Contains all the content, cross-referenced and linked.  |
| [`phishdestroy/destroylist`](https://github.com/phishdestroy/destroylist) | 1846 | active | Real-time phishing & scam domain blocklist - 205k+ curated threats, 1M+ community, free API, multiple formats |
| [`Jon-Becker/heimdall-rs`](https://github.com/Jon-Becker/heimdall-rs) | 1648 | active | Heimdall is an advanced EVM smart contract toolkit specializing in bytecode analysis and extracting information from unverified contracts. |
| [`pcaversaccio/reentrancy-attacks`](https://github.com/pcaversaccio/reentrancy-attacks) | 1625 | active | A chronological and (hopefully) complete list of reentrancy attacks to date. |
| [`sigp/solidity-security-blog`](https://github.com/sigp/solidity-security-blog) | 1524 | active | Comprehensive list of known attack vectors and common anti-patterns |
| [`crytic/awesome-ethereum-security`](https://github.com/crytic/awesome-ethereum-security) | 1485 | active | A curated list of awesome Ethereum security references |
| [`Dropnation/contract-fuzzer`](https://github.com/Dropnation/contract-fuzzer) | 1414 | active | Ethereum smart contract fuzzer |
| [`dethcrypto/dethcode`](https://github.com/dethcrypto/dethcode) | 1367 | active | View source of deployed Ethereum contracts in VSCode |
| [`pashov/skills`](https://github.com/pashov/skills) | 1324 | active | Pashov Audit Group Skills |
| [`TokenMarketNet/smart-contracts`](https://github.com/TokenMarketNet/smart-contracts) | 1321 | active | Ethereum smart contracts for security and utility tokens |

## blue-team

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`wazuh/wazuh`](https://github.com/wazuh/wazuh) | 17858 | active | Wazuh - The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads. |
| [`SigmaHQ/sigma`](https://github.com/SigmaHQ/sigma) | 11545 | active | Main Sigma Rule Repository |
| [`Graylog2/graylog2-server`](https://github.com/Graylog2/graylog2-server) | 8182 | active | Free and open log management |
| [`beenuar/AiSOC`](https://github.com/beenuar/AiSOC) | 7335 | active | Open-source AI Security Operations Center: alert fusion, LLM-agent triage, MITRE ATT&CK investigation, and a replayable decision ledger for every agent step. Self-hostabl |
| [`ihebski/DefaultCreds-cheat-sheet`](https://github.com/ihebski/DefaultCreds-cheat-sheet) | 6831 | active | One place for all the default credentials to assist the Blue/Red teamers identifying devices with default password 🛡️ |
| [`decalage2/awesome-security-hardening`](https://github.com/decalage2/awesome-security-hardening) | 6618 | active | A collection of awesome security hardening guides, tools and other resources |
| [`Trusted-AI/adversarial-robustness-toolbox`](https://github.com/Trusted-AI/adversarial-robustness-toolbox) | 6326 | active | Adversarial Robustness Toolbox (ART) - Python Library for Machine Learning Security - Evasion, Poisoning, Extraction, Inference - Red and Blue Teams |
| [`fabacab/awesome-cybersecurity-blueteam`](https://github.com/fabacab/awesome-cybersecurity-blueteam) | 5674 | active | :computer:🛡️ A curated collection of awesome resources, tools, and other shiny things for cybersecurity blue teams. |
| [`A-poc/BlueTeam-Tools`](https://github.com/A-poc/BlueTeam-Tools) | 4929 | active | Tools and Techniques for Blue Team / Incident Response |
| [`jiwoochris/artex-global`](https://github.com/jiwoochris/artex-global) | 4397 | active | Global edition of ARTEX - an LLM multi-agent autonomous penetration-testing framework. English default, switchable to Korean/Chinese/Spanish/Japanese. For authorized, def |
| [`SpinalHDL/VexRiscv`](https://github.com/SpinalHDL/VexRiscv) | 3312 | active | A FPGA friendly 32 bit RISC-V CPU implementation |
| [`mikeroyal/Digital-Forensics-Guide`](https://github.com/mikeroyal/Digital-Forensics-Guide) | 3241 | active | Digital Forensics Guide. Learn all about Digital Forensics, Computer Forensics, Mobile device Forensics, Network Forensics, and Database Forensics. |
| [`cisagov/RedEye`](https://github.com/cisagov/RedEye) | 2767 | archived | RedEye is a visual analytic tool supporting Red & Blue Team operations |
| [`outflanknl/RedELK`](https://github.com/outflanknl/RedELK) | 2692 | active | Red Team's SIEM - tool for Red Teams used for tracking and alarming about Blue Team activities as well as better usability in long term operations. |
| [`yogsec/Hacking-Tools`](https://github.com/yogsec/Hacking-Tools) | 2576 | active | A curated list of 900+ penetration testing and ethical hacking tools. |
| [`rabbitstack/fibratus`](https://github.com/rabbitstack/fibratus) | 2570 | active | Security sensor for realtime threat detection and protection |
| [`VictoriaMetrics/VictoriaLogs`](https://github.com/VictoriaMetrics/VictoriaLogs) | 2540 | active | Fast and easy to use database for logs, which can efficiently handle terabytes of logs |
| [`ucb-bar/chipyard`](https://github.com/ucb-bar/chipyard) | 2467 | active | An Agile RISC-V SoC Design Framework with in-order cores, out-of-order cores, accelerators, and more |
| [`stnolting/neorv32`](https://github.com/stnolting/neorv32) | 2319 | active | 🖥️ A small, customizable and extensible MCU-class 32-bit RISC-V soft-core CPU and microcontroller-like SoC written in platform-independent VHDL. |
| [`mozilla/MozDef`](https://github.com/mozilla/MozDef) | 2155 | archived | DEPRECATED - MozDef: Mozilla Enterprise Defense Platform |

## recon

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`sherlock-project/sherlock`](https://github.com/sherlock-project/sherlock) | 115651 | active | Hunt down social media accounts by username across social networks |
| [`soxoj/maigret`](https://github.com/soxoj/maigret) | 41155 | active | 🕵️‍♂️ Collect a dossier on a person by username from 6K websites |
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`lissy93/web-check`](https://github.com/lissy93/web-check) | 35561 | active | 🕵️‍♂️ All-in-one OSINT tool for analysing any website |
| [`jivoi/awesome-osint`](https://github.com/jivoi/awesome-osint) | 31320 | active | :scream: A curated list of amazingly awesome OSINT |
| [`smicallef/spiderfoot`](https://github.com/smicallef/spiderfoot) | 24831 | active | SpiderFoot automates OSINT for threat intelligence and mapping your attack surface. |
| [`qeeqbox/social-analyzer`](https://github.com/qeeqbox/social-analyzer) | 24601 | active | API, CLI, and Web App for analyzing and finding a person's profile in 1000 social media \ websites |
| [`gildas-lormeau/SingleFile`](https://github.com/gildas-lormeau/SingleFile) | 22980 | active | Web Extension for saving a faithful copy of a complete web page in a single HTML file |
| [`HunxByts/GhostTrack`](https://github.com/HunxByts/GhostTrack) | 20837 | active | Useful tool to track location or mobile number |
| [`mxrch/GHunt`](https://github.com/mxrch/GHunt) | 19889 | active | 🕵️‍♂️ Offensive Google framework. |
| [`sundowndev/phoneinfoga`](https://github.com/sundowndev/phoneinfoga) | 18651 | active | Information gathering framework for phone numbers |
| [`laramies/theHarvester`](https://github.com/laramies/theHarvester) | 18382 | active | E-mails, subdomains and names Harvester - OSINT  |
| [`twintproject/twint`](https://github.com/twintproject/twint) | 16418 | archived | An advanced Twitter scraping & OSINT tool written in Python that doesn't use Twitter's API, allowing you to scrape a user's followers, following, Tweets and more while ev |
| [`Datalux/Osintgram`](https://github.com/Datalux/Osintgram) | 15734 | active | Osintgram is a OSINT tool on Instagram. It offers an interactive shell to perform analysis on Instagram account of any users by its nickname |
| [`megadose/holehe`](https://github.com/megadose/holehe) | 15733 | active | holehe allows you to check if the mail is used on different sites like twitter, instagram and will retrieve information on sites with the forgotten password function. |
| [`owasp-amass/amass`](https://github.com/owasp-amass/amass) | 15547 | active | In-depth attack surface mapping and asset discovery |
| [`projectdiscovery/subfinder`](https://github.com/projectdiscovery/subfinder) | 14845 | active | Fast passive subdomain enumeration tool. |
| [`instaloader/instaloader`](https://github.com/instaloader/instaloader) | 13983 | active | Download pictures (or videos) along with their captions and other metadata from Instagram. |
| [`s0md3v/Photon`](https://github.com/s0md3v/Photon) | 13380 | active | Incredibly fast crawler designed for OSINT. |
| [`lockfale/OSINT-Framework`](https://github.com/lockfale/OSINT-Framework) | 12495 | active | OSINT Framework |

## mobile

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`MobSF/Mobile-Security-Framework-MobSF`](https://github.com/MobSF/Mobile-Security-Framework-MobSF) | 22292 | active | Mobile Security Framework (MobSF) is an automated, all-in-one mobile application (Android/iOS/Windows) pen-testing, malware analysis and security assessment framework cap |
| [`Hacker0x01/hacker101`](https://github.com/Hacker0x01/hacker101) | 14651 | active | Source code for Hacker101.com - a free online web and mobile security class. |
| [`OWASP/mastg`](https://github.com/OWASP/mastg) | 13367 | active | The OWASP Mobile Application Security Testing Guide (MASTG) is a comprehensive manual for mobile app security testing and reverse engineering. It describes technical proc |
| [`firerpa/lamda`](https://github.com/firerpa/lamda) | 8759 | active | Android Full-Stack Device Control Platform: WebRTC/H.264 remote desktop, UI/OCR/image-matching automation, one-click MITM, built-in Frida, proxy/VPN/frp/P2P networking, M |
| [`dwisiswant0/apkleaks`](https://github.com/dwisiswant0/apkleaks) | 6399 | active | Scanning APK file for URIs, endpoints & secrets. |
| [`qybaihe/bla`](https://github.com/qybaihe/bla) | 6398 | active | 🛡️ BLA (bla-agents / Blind LLM Agents) - Open-source Blue Defense MCP server & CLI protecting JS/TS, Electron ASAR, WASM, Python, Native Binaries, SQLite, GGUF/Safetensor |
| [`alphaSeclab/awesome-reverse-engineering`](https://github.com/alphaSeclab/awesome-reverse-engineering) | 5224 | active | Reverse Engineering Resources About All Platforms(Windows/Linux/macOS/Android/iOS/IoT) And Every Aspect! (More than 3500 open source tools and 2300 posts&videos) |
| [`urbanadventurer/Android-PIN-Bruteforce`](https://github.com/urbanadventurer/Android-PIN-Bruteforce) | 4899 | active | Unlock an Android phone (or device) by bruteforcing the lockscreen PIN. Turn your Kali Nethunter phone into a bruteforce PIN cracker for Android devices! (no root, no adb |
| [`charles2gan/GDA-android-reversing-Tool`](https://github.com/charles2gan/GDA-android-reversing-Tool) | 4879 | active | the fastest and most powerful android decompiler(native tool working without Java VM) for the APK, DEX, ODEX, OAT, JAR, AAR, and CLASS file. which supports malicious beha |
| [`ax/apk.sh`](https://github.com/ax/apk.sh) | 3871 | active | Makes reverse engineering Android apps easier, automating repetitive tasks like pulling, decoding, rebuilding and patching an APK. |
| [`Verlintas/NOIR`](https://github.com/Verlintas/NOIR) | 3683 | active | 手机隐私审计：用 adb 扫出 Android 应用的权限、上报端点与数据流向，并给出可回滚的处置。Desktop Android leak audit: permissions, endpoints, data flows, reversible fixes. |
| [`vaib25vicky/awesome-mobile-security`](https://github.com/vaib25vicky/awesome-mobile-security) | 3582 | active | An effort to build a single place for all useful android and iOS security related stuff. All references and tools belong to their respective owners. I'm just maintaining  |
| [`reacher-z/Awesome-REA`](https://github.com/reacher-z/Awesome-REA) | 3324 | active | Curated tools, case studies and papers for reverse engineering websites, games, apps, binaries, firmware and protocols. Source-reviewed, English and Chinese. |
| [`m0bilesecurity/RMS-Runtime-Mobile-Security`](https://github.com/m0bilesecurity/RMS-Runtime-Mobile-Security) | 3153 | active | Runtime Mobile Security (RMS) 📱🔥  - is a powerful web interface that helps you to manipulate Android and iOS Apps at Runtime |
| [`Impact-I/reFlutter`](https://github.com/Impact-I/reFlutter) | 2807 | active | Flutter Reverse Engineering Framework |
| [`iddoeldor/frida-snippets`](https://github.com/iddoeldor/frida-snippets) | 2537 | active | Hand-crafted Frida examples |
| [`REAndroid/APKEditor`](https://github.com/REAndroid/APKEditor) | 2446 | active | Powerful android apk editor - aapt/aapt2 independent |
| [`saeidshirazi/awesome-android-security`](https://github.com/saeidshirazi/awesome-android-security) | 2044 | active | A curated list of Android Security materials and resources For Pentesters and Bug Hunters |
| [`ashishb/osx-and-ios-security-awesome`](https://github.com/ashishb/osx-and-ios-security-awesome) | 1780 | active | OSX and iOS related security tools |
| [`B3nac/Android-Reports-and-Resources`](https://github.com/B3nac/Android-Reports-and-Resources) | 1721 | active | A big list of Android Hackerone disclosed reports and other resources.  |

## cloud-security

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`mukul975/Anthropic-Cybersecurity-Skills`](https://github.com/mukul975/Anthropic-Cybersecurity-Skills) | 36533 | active | 817 structured cybersecurity skills for AI agents · Mapped to 6 frameworks: MITRE ATT&CK, NIST CSF 2.0, MITRE ATLAS, D3FEND, NIST AI RMF & MITRE F3 (Fight Fraud) · agents |
| [`wazuh/wazuh`](https://github.com/wazuh/wazuh) | 17858 | active | Wazuh - The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads. |
| [`bridgecrewio/checkov`](https://github.com/bridgecrewio/checkov) | 9165 | active | Prevent cloud misconfigurations and find vulnerabilities during build-time in infrastructure as code, container images and open source packages with Checkov by Bridgecrew |
| [`aquasecurity/kube-bench`](https://github.com/aquasecurity/kube-bench) | 8264 | active | Checks whether Kubernetes is deployed according to security best practices as defined in the CIS Kubernetes Benchmark |
| [`techiescamp/kubernetes-learning-path`](https://github.com/techiescamp/kubernetes-learning-path) | 7700 | active | A roadmap to learn Kubernetes from scratch (Beginner to Advanced level) |
| [`madhuakula/kubernetes-goat`](https://github.com/madhuakula/kubernetes-goat) | 5962 | active | Kubernetes Goat is a "Vulnerable by Design" cluster environment to learn and practice Kubernetes security using an interactive hands-on playground 🚀 |
| [`RhinoSecurityLabs/pacu`](https://github.com/RhinoSecurityLabs/pacu) | 5403 | active | The AWS exploitation framework, designed for testing the security of Amazon Web Services environments. |
| [`tenable/terrascan`](https://github.com/tenable/terrascan) | 5210 | archived | Detect compliance and security violations across Infrastructure as Code to mitigate risk before provisioning cloud native infrastructure. |
| [`jassics/security-study-plan`](https://github.com/jassics/security-study-plan) | 5135 | active | Complete Practical Study Plan to become a successful cybersecurity engineer based on roles like Pentest, AppSec, Cloud Security, DevSecOps and so on... |
| [`Hack-with-Github/Free-Security-eBooks`](https://github.com/Hack-with-Github/Free-Security-eBooks) | 5028 | archived | Free Security and Hacking eBooks |
| [`cdk-team/CDK`](https://github.com/cdk-team/CDK) | 4797 | active | 📦  Make security testing of K8s, Docker, and Containerd easier. |
| [`Netflix/security_monkey`](https://github.com/Netflix/security_monkey) | 4364 | archived | Security Monkey monitors AWS, GCP, OpenStack, and GitHub orgs for assets and their changes over time. |
| [`Netflix/consoleme`](https://github.com/Netflix/consoleme) | 3192 | archived | A Central Control Plane for AWS Permissions and Access |
| [`Hacking-the-Cloud/hackingthe.cloud`](https://github.com/Hacking-the-Cloud/hackingthe.cloud) | 2797 | active | An encyclopedia for offensive and defensive security knowledge in cloud native technologies. |
| [`BishopFox/cloudfox`](https://github.com/BishopFox/cloudfox) | 2631 | active | Automating situational awareness for cloud penetration tests. |
| [`4ndersonLin/awesome-cloud-security`](https://github.com/4ndersonLin/awesome-cloud-security) | 2523 | active | 🛡️ Awesome Cloud Security Resources ⚔️ |
| [`DataDog/stratus-red-team`](https://github.com/DataDog/stratus-red-team) | 2455 | active | :cloud: :zap: Granular, Actionable Adversary Emulation for the Cloud |
| [`salesforce/cloudsplaining`](https://github.com/salesforce/cloudsplaining) | 2261 | active | Cloudsplaining is an AWS IAM Security Assessment tool that identifies violations of least privilege and generates a risk-prioritized report. |
| [`salesforce/policy_sentry`](https://github.com/salesforce/policy_sentry) | 2173 | active | IAM Least Privilege Policy Generator |
| [`ChiefGyk3D/Clouds-Against-Humanity`](https://github.com/ChiefGyk3D/Clouds-Against-Humanity) | 2151 | active | Learn cloud security by building it! An open-source homelab teaching Zero Trust, DevSecOps, IaC, and cloud architecture from the ground up. |

## toolkit

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`trimstray/the-book-of-secret-knowledge`](https://github.com/trimstray/the-book-of-secret-knowledge) | 260406 | active | A collection of inspiring lists, manuals, cheatsheets, blogs, hacks, one-liners, cli/web tools and more. |
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`x64dbg/x64dbg`](https://github.com/x64dbg/x64dbg) | 50365 | active | An open-source user mode debugger for Windows. Optimized for reverse engineering and malware analysis. |
| [`aquasecurity/trivy`](https://github.com/aquasecurity/trivy) | 39609 | active | Find vulnerabilities, misconfigurations, secrets, SBOM in containers, Kubernetes, code repositories, clouds and more |
| [`lissy93/web-check`](https://github.com/lissy93/web-check) | 35561 | active | 🕵️‍♂️ All-in-one OSINT tool for analysing any website |
| [`vxcontrol/pentagi`](https://github.com/vxcontrol/pentagi) | 34785 | active | Fully autonomous AI Agents system capable of performing complex penetration testing tasks |
| [`OWASP/CheatSheetSeries`](https://github.com/OWASP/CheatSheetSeries) | 34155 | active | The OWASP Cheat Sheet Series was created to provide a concise collection of high value information on specific application security topics. |
| [`gitleaks/gitleaks`](https://github.com/gitleaks/gitleaks) | 31252 | active | Find secrets with Gitleaks 🔑 |
| [`Infisical/infisical`](https://github.com/Infisical/infisical) | 30674 | active | Infisical is the open-source platform for securing secrets, certificates, access, and AI Agents. |
| [`trufflesecurity/trufflehog`](https://github.com/trufflesecurity/trufflehog) | 29572 | active | Find, verify, and analyze leaked credentials |
| [`NVIDIA/SkillSpector`](https://github.com/NVIDIA/SkillSpector) | 27230 | active | Security scanner for AI agent skills. Detect vulnerabilities, malicious patterns, security risks, prompt injection, data exfiltration, and supply-chain risks in Claude Co |
| [`smicallef/spiderfoot`](https://github.com/smicallef/spiderfoot) | 24831 | active | SpiderFoot automates OSINT for threat intelligence and mapping your attack surface. |
| [`qeeqbox/social-analyzer`](https://github.com/qeeqbox/social-analyzer) | 24601 | active | API, CLI, and Web App for analyzing and finding a person's profile in 1000 social media \ websites |
| [`lissy93/personal-security-checklist`](https://github.com/lissy93/personal-security-checklist) | 22851 | active | 🔒 A compiled checklist of 300+ tips for protecting digital security and privacy in 2026 |
| [`bee-san/RustScan`](https://github.com/bee-san/RustScan) | 20789 | active | 🤖 The Modern Port Scanner 🤖 |
| [`motdotla/dotenv`](https://github.com/motdotla/dotenv) | 20572 | active | Loads environment variables from .env for nodejs projects. |
| [`fail2ban/fail2ban`](https://github.com/fail2ban/fail2ban) | 19083 | active | Daemon to ban hosts that cause multiple authentication errors |
| [`wazuh/wazuh`](https://github.com/wazuh/wazuh) | 17858 | active | Wazuh - The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads. |
| [`CISOfy/lynis`](https://github.com/CISOfy/lynis) | 16739 | active | Lynis - Security auditing tool for Linux, macOS, and UNIX-based systems. Assists with compliance testing (HIPAA/ISO27001/PCI DSS) and system hardening. Agentless, and ins |

## hardening

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`imthenachoman/How-To-Secure-A-Linux-Server`](https://github.com/imthenachoman/How-To-Secure-A-Linux-Server) | 32089 | active | An evolving how-to guide for securing a Linux server. |
| [`wazuh/wazuh`](https://github.com/wazuh/wazuh) | 17858 | active | Wazuh - The Open Source Security Platform. Unified XDR and SIEM protection for endpoints and cloud workloads. |
| [`CISOfy/lynis`](https://github.com/CISOfy/lynis) | 16739 | active | Lynis - Security auditing tool for Linux, macOS, and UNIX-based systems. Assists with compliance testing (HIPAA/ISO27001/PCI DSS) and system hardening. Agentless, and ins |
| [`prowler-cloud/prowler`](https://github.com/prowler-cloud/prowler) | 15301 | active | This repo hosts Prowler OSS (CLI and Local Server). Prowler is the world’s most widely used open-source cloud security platform that automates security and compliance acr |
| [`future-architect/vuls`](https://github.com/future-architect/vuls) | 12363 | active | Agent-less vulnerability scanner for Linux, FreeBSD, Container, WordPress, Programming language libraries, Network devices |
| [`trimstray/the-practical-linux-hardening-guide`](https://github.com/trimstray/the-practical-linux-hardening-guide) | 10923 | active | This guide details creating a secure Linux production system. OpenSCAP (C2S/CIS, STIG). |
| [`decalage2/awesome-security-hardening`](https://github.com/decalage2/awesome-security-hardening) | 6618 | active | A collection of awesome security hardening guides, tools and other resources |
| [`undergroundwires/privacy.sexy`](https://github.com/undergroundwires/privacy.sexy) | 6187 | active | Open-source tool to enforce privacy & security best-practices on Windows, macOS and Linux, because privacy is sexy |
| [`HotCakeX/Harden-Windows-Security`](https://github.com/HotCakeX/Harden-Windows-Security) | 4864 | active | Harden Windows Safely, Securely using Official Supported Microsoft methods and proper explanation / Always up-to-date and works with the latest build of Windows / Provide |
| [`immunant/c2rust`](https://github.com/immunant/c2rust) | 4848 | active | Migrate C code to Rust |
| [`pyllyukko/user.js`](https://github.com/pyllyukko/user.js) | 2897 | active | user.js -- Firefox configuration hardening |
| [`ComplianceAsCode/content`](https://github.com/ComplianceAsCode/content) | 2847 | active | Security automation content in SCAP, Bash, Ansible, and other formats |
| [`sbousseaden/EVTX-ATTACK-SAMPLES`](https://github.com/sbousseaden/EVTX-ATTACK-SAMPLES) | 2651 | active | Windows Events Attack Samples |
| [`rabbitstack/fibratus`](https://github.com/rabbitstack/fibratus) | 2570 | active | Security sensor for realtime threat detection and protection |
| [`konstruktoid/hardening`](https://github.com/konstruktoid/hardening) | 1860 | active | Hardening Ubuntu. Systemd edition. |
| [`intika/Librefox`](https://github.com/intika/Librefox) | 1783 | active | Librefox: Firefox with privacy enhancements |
| [`google/sandboxed-api`](https://github.com/google/sandboxed-api) | 1774 | active | Generate sandboxes for C/C++ libraries automatically |
| [`google/go-safeweb`](https://github.com/google/go-safeweb) | 1492 | archived | Secure-by-default HTTP servers in Go. |
| [`USBGuard/usbguard`](https://github.com/USBGuard/usbguard) | 1407 | active | USBGuard is a software framework for implementing USB device authorization policies (what kind of USB devices are authorized) as well as method of use policies (how a USB |
| [`mikeroyal/Linux-Guide`](https://github.com/mikeroyal/Linux-Guide) | 1384 | active | Linux Guide. Learn about Linux Hardware vendors, Linux in the Cloud, Desktop Environments, Window Mangers, Linux Distributions, Linux Security, Graphics (AMD/NVIDIA/Intel |

## ghidra

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`qybaihe/bla`](https://github.com/qybaihe/bla) | 6398 | active | 🛡️ BLA (bla-agents / Blind LLM Agents) - Open-source Blue Defense MCP server & CLI protecting JS/TS, Electron ASAR, WASM, Python, Native Binaries, SQLite, GGUF/Safetensor |
| [`bethington/ghidra-mcp`](https://github.com/bethington/ghidra-mcp) | 6322 | active | Ghidra MCP Server - 200+ MCP tools for AI-powered reverse engineering. GUI plugin + headless server, lazy tool loading, convention enforcement, batch operations, Ghidra S |
| [`alphaSeclab/awesome-reverse-engineering`](https://github.com/alphaSeclab/awesome-reverse-engineering) | 5224 | active | Reverse Engineering Resources About All Platforms(Windows/Linux/macOS/Android/iOS/IoT) And Every Aspect! (More than 3500 open source tools and 2300 posts&videos) |
| [`joernio/joern`](https://github.com/joernio/joern) | 3657 | active | Open-source code analysis platform for C/C++/Java/Binary/Javascript/Python/Kotlin based on code property graphs. Discord https://discord.gg/vv4MH284Hc |
| [`reacher-z/Awesome-REA`](https://github.com/reacher-z/Awesome-REA) | 3324 | active | Curated tools, case studies and papers for reverse engineering websites, games, apps, binaries, firmware and protocols. Source-reviewed, English and Chinese. |
| [`decompiler-explorer/decompiler-explorer`](https://github.com/decompiler-explorer/decompiler-explorer) | 2733 | active | Decompiler Explorer! Compare tools on the forefront of static analysis, now in your web browser! |
| [`Dryxio/reagent`](https://github.com/Dryxio/reagent) | 2458 | active | Reconstruct and validate C/C++ code from compiled programs with AI. |
| [`bootleg/ret-sync`](https://github.com/bootleg/ret-sync) | 2416 | active | ret-sync is a set of plugins that helps to synchronize a debugging session (WinDbg/GDB/LLDB/OllyDbg2/x64dbg) with IDA/Ghidra/Binary Ninja disassemblers. |
| [`morluto/awesome-reverse-engineering`](https://github.com/morluto/awesome-reverse-engineering) | 2273 | active | Curated reverse-engineering tools and learning resources for binaries, apps, firmware, formats, and protocols. |
| [`KeenSecurityLab/BinAbsInspector`](https://github.com/KeenSecurityLab/BinAbsInspector) | 1685 | active | BinAbsInspector: Vulnerability Scanner for Binaries |
| [`fr0gger/awesome-ida-x64-olly-plugin`](https://github.com/fr0gger/awesome-ida-x64-olly-plugin) | 1609 | active | A curated list of IDA x64DBG, Ghidra and OllyDBG plugins.  |
| [`OpenDriver2/REDRIVER2`](https://github.com/OpenDriver2/REDRIVER2) | 1471 | active | Driver 2 Playstation game reverse engineering effort |
| [`AllsafeCyberSecurity/awesome-ghidra`](https://github.com/AllsafeCyberSecurity/awesome-ghidra) | 1466 | active | A curated list of awesome Ghidra materials |
| [`LING71671/open-reverselab`](https://github.com/LING71671/open-reverselab) | 1386 | active | Open-source AI reverse-engineering workspace with a desktop app, red / blue / both analysis, parallel Goals, a security knowledge base, and MCP tools for Ghidra, Frida, x |
| [`fkie-cad/cwe_checker`](https://github.com/fkie-cad/cwe_checker) | 1363 | active | cwe_checker finds vulnerable patterns in binary executables |
| [`ansjdnakjdnajkd/iOS`](https://github.com/ansjdnakjdnajkd/iOS) | 1339 | active | Most usable tools for iOS penetration testing |
| [`amruth-sn/kong`](https://github.com/amruth-sn/kong) | 1247 | active | The world's first agentic reverse engineer. |
| [`google/binexport`](https://github.com/google/binexport) | 1215 | active | Export disassemblies into Protocol Buffers |
| [`ghidraninja/ghidra_scripts`](https://github.com/ghidraninja/ghidra_scripts) | 1186 | active | Scripts for the Ghidra software reverse engineering suite. |
| [`Zfinix/eleven`](https://github.com/Zfinix/eleven) | 1016 | active | Agent-first reverse engineering for macOS, Windows and Linux: any ACP agent reads, renames and patches binaries, Android apps and GPU kernels next to you. |

## ida-binja

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`radareorg/radare2`](https://github.com/radareorg/radare2) | 25482 | active | UNIX-like reverse engineering framework and command-line toolset |
| [`mrexodia/ida-pro-mcp`](https://github.com/mrexodia/ida-pro-mcp) | 13542 | active | AI-powered reverse engineering assistant that bridges IDA Pro with language models through MCP. |
| [`alphaSeclab/awesome-reverse-engineering`](https://github.com/alphaSeclab/awesome-reverse-engineering) | 5224 | active | Reverse Engineering Resources About All Platforms(Windows/Linux/macOS/Android/iOS/IoT) And Every Aspect! (More than 3500 open source tools and 2300 posts&videos) |
| [`google/bindiff`](https://github.com/google/bindiff) | 3246 | active | Quickly find differences and similarities in disassembled code |
| [`decompiler-explorer/decompiler-explorer`](https://github.com/decompiler-explorer/decompiler-explorer) | 2733 | active | Decompiler Explorer! Compare tools on the forefront of static analysis, now in your web browser! |
| [`mandiant/flare-ida`](https://github.com/mandiant/flare-ida) | 2452 | archived | IDA Pro utilities from FLARE team |
| [`bootleg/ret-sync`](https://github.com/bootleg/ret-sync) | 2416 | active | ret-sync is a set of plugins that helps to synchronize a debugging session (WinDbg/GDB/LLDB/OllyDbg2/x64dbg) with IDA/Ghidra/Binary Ninja disassemblers. |
| [`KasperskyLab/hrtng`](https://github.com/KasperskyLab/hrtng) | 1974 | active | IDA Pro plugin with a rich set of features: decryption, deobfuscation, patching, lib code recognition and various pseudocode transformations |
| [`airbus-seclab/bincat`](https://github.com/airbus-seclab/bincat) | 1881 | active | Binary code static analyser, with IDA integration. Performs value and taint analysis, type reconstruction, use-after-free and double-free detection |
| [`illera88/Ponce`](https://github.com/illera88/Ponce) | 1634 | active | IDA 2016 plugin contest winner! Symbolic Execution just one-click away! |
| [`igogo-x86/HexRaysPyTools`](https://github.com/igogo-x86/HexRaysPyTools) | 1544 | active | IDA Pro plugin which improves work with HexRays decompiler and helps in process of reconstruction structures and classes |
| [`hteso/iaito`](https://github.com/hteso/iaito) | 1461 | active | This project has been moved to: |
| [`nowsecure/r2frida`](https://github.com/nowsecure/r2frida) | 1457 | active | Radare2 and Frida better together. |
| [`L4ys/LazyIDA`](https://github.com/L4ys/LazyIDA) | 1448 | active | Make your IDA Lazy! |
| [`WPeace-HcH/WPeGPT`](https://github.com/WPeace-HcH/WPeGPT) | 1425 | active | An IDA plugin for binary file analysis, powered by AI models such as OpenAI and DeepSeek. |
| [`Vector35/binaryninja-api`](https://github.com/Vector35/binaryninja-api) | 1348 | active | Public API, examples, documentation and issues for Binary Ninja |
| [`google/binexport`](https://github.com/google/binexport) | 1215 | active | Export disassemblies into Protocol Buffers |
| [`REhints/efiXplorer`](https://github.com/REhints/efiXplorer) | 1149 | active | IDA plugin and loader for UEFI firmware analysis and reverse engineering automation |
| [`ioncodes/idacode`](https://github.com/ioncodes/idacode) | 997 | active | An integration for IDA and VS Code which connects both to easily execute and debug IDAPython scripts. |
| [`cyberkaida/reverse-engineering-assistant`](https://github.com/cyberkaida/reverse-engineering-assistant) | 975 | active | MCP server for reverse engineering tasks in Ghidra 👩‍💻 |

## wireless

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`bettercap/bettercap`](https://github.com/bettercap/bettercap) | 20338 | active | The Swiss Army knife for 802.11, BLE, HID, CAN-bus, IPv4 and IPv6 networks reconnaissance and MITM attacks. |
| [`SpacehuhnTech/esp8266_deauther`](https://github.com/SpacehuhnTech/esp8266_deauther) | 15119 | active | Affordable WiFi hacking platform for testing and learning |
| [`brannondorsey/wifi-cracking`](https://github.com/brannondorsey/wifi-cracking) | 12764 | active | Crack WPA/WPA2 Wi-Fi Routers with Airodump-ng and Aircrack-ng/Hashcat |
| [`EmenstaNougat/ESP32-BlueJammer`](https://github.com/EmenstaNougat/ESP32-BlueJammer) | 8971 | active | The ESP32-BlueJammer (Bluetooth jammer, BLE jammer, WiFi jammer, RC jammer) disrupts 2.4GHz communications. Using an ESP32 and nRF24 modules, it generates noise and unnec |
| [`geo-tp/ESP32-Bit-Pirate`](https://github.com/geo-tp/ESP32-Bit-Pirate) | 6412 | active | A Hardware Hacking Tool with Web-Based CLI That Speaks Every Protocol  |
| [`derv82/wifit3`](https://github.com/derv82/wifit3) | 3677 | active | Wifite but USB-only & cross-platform. |
| [`V33RU/awesome-connected-things-sec`](https://github.com/V33RU/awesome-connected-things-sec) | 3582 | active | A Curated list of Security Resources for all connected things |
| [`cirosantilli/china-dictatorship`](https://github.com/cirosantilli/china-dictatorship) | 3363 | active | 反中共政治宣传库。Anti Chinese government propaganda. 住在中国真名用户的网友请别给星星，不然你要被警察请喝茶。常见问答集，新闻集和饭店和音乐建议。卐习万岁卐。冠状病毒审查郝海东新疆改造中心六四事件法轮功 996.ICU709大抓捕巴拿马文件邓家贵低端人口西藏骚乱。Friends who live in  |
| [`P0cL4bs/WiFi-Pumpkin-deprecated`](https://github.com/P0cL4bs/WiFi-Pumpkin-deprecated) | 3150 | archived | DEPRECATED, wifipumpkin3 -> https://github.com/P0cL4bs/wifipumpkin3 |
| [`7h30th3r0n3/Evil-M5Project`](https://github.com/7h30th3r0n3/Evil-M5Project) | 2924 | active | Evil-M5Project is an innovative tool developed for ethical  hacking and exploration of WiFi networks. It's compatible with Cardputer, Atoms3, Fire, core2. You can scan, m |
| [`ankit0183/Wifi-Hacking`](https://github.com/ankit0183/Wifi-Hacking) | 2809 | active | Cyber Security Tool For Hacking Wireless Connections Using Built-In Kali Tools. Supports All Securities (WEP, WPS, WPA, WPA2/TKIP/IES) |
| [`calebmadrigal/trackerjacker`](https://github.com/calebmadrigal/trackerjacker) | 2745 | active | Like nmap for mapping wifi networks you're not connected to, plus device tracking |
| [`k4m4/kickthemout`](https://github.com/k4m4/kickthemout) | 2686 | active | 💤 Kick devices off your network by performing an ARP Spoof attack. |
| [`koutto/pi-pwnbox-rogueap`](https://github.com/koutto/pi-pwnbox-rogueap) | 2119 | active | Homemade Pwnbox :rocket: / Rogue AP :satellite: based on Raspberry Pi - WiFi Hacking Cheatsheets + MindMap :bulb:  |
| [`gege-circle/.github`](https://github.com/gege-circle/.github) | 2117 | active | 这里是GitHub的草场，也是戈戈圈爱好者的交流地，主要讨论动漫、游戏、科技、人文、生活等所有话题，欢迎各位小伙伴们在此讨论趣事。This is GitHub grassland, and the community place for Gege circle lovers, mainly discusses anime, games,  |
| [`Ragnt/AngryOxide`](https://github.com/Ragnt/AngryOxide) | 2020 | active | 802.11 Attack Tool |
| [`JesseCHale/HaleHound-CYD`](https://github.com/JesseCHale/HaleHound-CYD) | 1939 | active | ESP32-DIV HaleHound Edition for Cheap Yellow Display - Multi-protocol offensive security toolkit |
| [`xdavidhu/mitmAP`](https://github.com/xdavidhu/mitmAP) | 1695 | archived | 📡 A python program to create a fake AP and sniff data. |
| [`xploitstech/Xteam`](https://github.com/xploitstech/Xteam) | 1372 | active | Xteam All in one Instagram,Android,phishing osint and wifi hacking tool available  |
| [`xchwarze/wifi-pineapple-cloner`](https://github.com/xchwarze/wifi-pineapple-cloner) | 1278 | active | Port WiFi Pineapple NANO/TETRA in generic hardware |

## ad

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`vitalysim/Awesome-Hacking-Resources`](https://github.com/vitalysim/Awesome-Hacking-Resources) | 17664 | active | A collection of hacking / penetration testing resources to make you better! |
| [`byt3bl33d3r/CrackMapExec`](https://github.com/byt3bl33d3r/CrackMapExec) | 9182 | archived | A swiss army knife for pentesting networks |
| [`Orange-Cyberdefense/GOAD`](https://github.com/Orange-Cyberdefense/GOAD) | 8647 | active | game of active directory |
| [`liamg/traitor`](https://github.com/liamg/traitor) | 7157 | active | :arrow_up: :skull_and_crossbones: :fire: Automatic Linux privesc via exploitation of low-hanging fruit e.g. gtfobins, pwnkit, dirty pipe, +w docker.sock |
| [`S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet`](https://github.com/S1ckB0y1337/Active-Directory-Exploitation-Cheat-Sheet) | 6815 | active | A cheat sheet that contains common enumeration and attack methods for Windows Active Directory. |
| [`xairy/linux-kernel-exploitation`](https://github.com/xairy/linux-kernel-exploitation) | 6696 | active | A collection of links related to Linux kernel security and exploitation |
| [`k8gege/K8tools`](https://github.com/k8gege/K8tools) | 6253 | active | K8工具集(内网渗透/提权工具/远程溢出/漏洞利用/端口扫描/密码破解/免杀/Exploit/RCE/LPE/0day/Shellcode/Payload/BypassUAC/OverFlow/WebShell/PenTest/hacking(Struts2/Zimbra/Weblogic/Tomcat/Apache/Jboss/DotN |
| [`rmusser01/Infosec_Reference`](https://github.com/rmusser01/Infosec_Reference) | 6045 | active | An Information Security Reference That Doesn't Suck; https://rmusser.net/git/admin-2/Infosec_Reference for non-MS Git hosted version. |
| [`Pennyw0rth/NetExec`](https://github.com/Pennyw0rth/NetExec) | 6040 | active | The Network Execution Tool |
| [`cdk-team/CDK`](https://github.com/cdk-team/CDK) | 4797 | active | 📦  Make security testing of K8s, Docker, and Containerd easier. |
| [`Threekiii/Awesome-Redteam`](https://github.com/Threekiii/Awesome-Redteam) | 4404 | active | 一个攻防知识库。A knowledge base for red teaming and offensive security. |
| [`theori-io/copy-fail-CVE-2026-31431`](https://github.com/theori-io/copy-fail-CVE-2026-31431) | 4089 | active | Copy Fail (CVE-2026-31431): 9-year-old Linux kernel LPE found by Theori's Xint Code |
| [`itm4n/PrivescCheck`](https://github.com/itm4n/PrivescCheck) | 4018 | active | Privilege Escalation Enumeration Script for Windows |
| [`diego-treitos/linux-smart-enumeration`](https://github.com/diego-treitos/linux-smart-enumeration) | 4014 | active | Linux enumeration tool for pentesting and CTFs with verbosity levels |
| [`S3cur3Th1sSh1t/WinPwn`](https://github.com/S3cur3Th1sSh1t/WinPwn) | 3706 | active | Automation for internal Windows Penetrationtest / AD-Security |
| [`Ignitetechnologies/Privilege-Escalation`](https://github.com/Ignitetechnologies/Privilege-Escalation) | 3653 | active | This cheasheet is aimed at the CTF Players and Beginners to help them understand the fundamentals of Privilege Escalation with examples. |
| [`JPCERTCC/LogonTracer`](https://github.com/JPCERTCC/LogonTracer) | 3305 | active | Investigate malicious Windows logon by visualizing and analyzing Windows event log |
| [`netwrix/pingcastle`](https://github.com/netwrix/pingcastle) | 3008 | active | PingCastle - Get Active Directory Security at 80% in 20% of the time |
| [`calebstewart/pwncat`](https://github.com/calebstewart/pwncat) | 2910 | archived | Fancy reverse and bind shell handler |

## ai-security

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`usestrix/strix`](https://github.com/usestrix/strix) | 76977 | active | Open-source AI penetration testing tool to find and fix your app’s vulnerabilities. |
| [`KeygraphHQ/shannon`](https://github.com/KeygraphHQ/shannon) | 52176 | active | Shannon is an AI pentester for web applications and APIs. It analyzes your source code, identifies attack vectors, and executes real exploits to prove vulnerabilities bef |
| [`The-Art-of-Hacking/h4cker`](https://github.com/The-Art-of-Hacking/h4cker) | 30308 | active | This repository is maintained by Omar Santos (@santosomar) and includes thousands of resources related to ethical hacking, bug bounties, digital forensics and incident re |
| [`NVIDIA/SkillSpector`](https://github.com/NVIDIA/SkillSpector) | 27230 | active | Security scanner for AI agent skills. Detect vulnerabilities, malicious patterns, security risks, prompt injection, data exfiltration, and supply-chain risks in Claude Co |
| [`elementalsouls/Claude-BugHunter`](https://github.com/elementalsouls/Claude-BugHunter) | 16733 | active | A Claude Code skill bundle for bug hunting and external red-team work - 82 skills, 15 slash commands, 681 disclosed-report patterns curated across 24 core vulnerability c |
| [`openai/codex-security`](https://github.com/openai/codex-security) | 14264 | active | OpenAI's Codex Security CLI and TypeScript SDK for finding, validating, and fixing security vulnerabilities. npm: https://www.npmjs.com/package/@openai/codex-security |
| [`LouisShark/chatgpt_system_prompt`](https://github.com/LouisShark/chatgpt_system_prompt) | 10862 | active | A collection of GPT system prompts and various prompt injection/leaking knowledge. |
| [`NVIDIA/garak`](https://github.com/NVIDIA/garak) | 10128 | active | the LLM vulnerability scanner |
| [`We5ter/Scanners-Box`](https://github.com/We5ter/Scanners-Box) | 9224 | active | The Ultimate Open-Source Security Arsenal for Hackers, Enterprises, and AI Agents--面向极客、企业与 AI 智能体的全域开源网络安全工具矩阵 |
| [`Tencent/AI-Infra-Guard`](https://github.com/Tencent/AI-Infra-Guard) | 7610 | active | A full-stack AI Red Teaming platform securing AI ecosystems via Agent Scan, Skills Scan, MCP scan, AI Infra scan and LLM jailbreak evaluation. |
| [`NVIDIA-NeMo/Guardrails`](https://github.com/NVIDIA-NeMo/Guardrails) | 7545 | active | NeMo Guardrails is an open-source library for applying programmable safety and security controls to LLM and agentic applications. |
| [`beenuar/AiSOC`](https://github.com/beenuar/AiSOC) | 7335 | active | Open-source AI Security Operations Center: alert fusion, LLM-agent triage, MITRE ATT&CK investigation, and a replayable decision ledger for every agent step. Self-hostabl |
| [`superagent-ai/superagent`](https://github.com/superagent-ai/superagent) | 6884 | active | Superagent protects your AI applications against prompt injections, data leaks, and harmful outputs. Embed safety directly into your app and prove compliance to your cust |
| [`FlorianBruniaux/claude-code-ultimate-guide`](https://github.com/FlorianBruniaux/claude-code-ultimate-guide) | 6590 | active | The most comprehensive Claude Code guide: agentic workflows, hooks, skills, MCP servers, quizzes, and production-ready templates. 430K+ lines. |
| [`qybaihe/bla`](https://github.com/qybaihe/bla) | 6398 | active | 🛡️ BLA (bla-agents / Blind LLM Agents) - Open-source Blue Defense MCP server & CLI protecting JS/TS, Electron ASAR, WASM, Python, Native Binaries, SQLite, GGUF/Safetensor |
| [`awarexone/Agentic-Bug-Hunter`](https://github.com/awarexone/Agentic-Bug-Hunter) | 6022 | active | AI-powered bug bounty hunting toolkit that works with or without subscription. |
| [`Giskard-AI/giskard-oss`](https://github.com/Giskard-AI/giskard-oss) | 5967 | active | 🐢 Open-Source Evaluation & Testing library for LLM Agents |
| [`nolabs-ai/nono`](https://github.com/nolabs-ai/nono) | 5070 | active | agent runtime security - zero trust, zero setup, zero latency agent sandboxing |
| [`microsoft/PyRIT`](https://github.com/microsoft/PyRIT) | 5020 | active | Open-source AI red teaming toolkit from Microsoft. Test generative AI systems with automated scans, adaptive multi-turn attacks, and a web UI. |
| [`theori-io/copy-fail-CVE-2026-31431`](https://github.com/theori-io/copy-fail-CVE-2026-31431) | 4089 | active | Copy Fail (CVE-2026-31431): 9-year-old Linux kernel LPE found by Theori's Xint Code |

## crypto

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`mitmproxy/mitmproxy`](https://github.com/mitmproxy/mitmproxy) | 47135 | active | An interactive TLS-capable intercepting HTTP proxy for penetration testers and software developers. |
| [`TheAlgorithms/JavaScript`](https://github.com/TheAlgorithms/JavaScript) | 34269 | active | Algorithms and Data Structures implemented in JavaScript for beginners, following best practices. |
| [`anoma/anoma`](https://github.com/anoma/anoma) | 33526 | active | Reference implementation of Anoma |
| [`nginx/nginx`](https://github.com/nginx/nginx) | 32800 | active | The official NGINX Open Source repository. |
| [`openssl/openssl`](https://github.com/openssl/openssl) | 31508 | active | General purpose TLS and crypto library |
| [`bee-san/Ciphey`](https://github.com/bee-san/Ciphey) | 21782 | active | ⚡ Automatically decrypt encryptions without knowing the key or cipher, decode encodings, and crack hashes ⚡ |
| [`amark/gun`](https://github.com/amark/gun) | 19167 | active | An open source cybersecurity protocol for syncing decentralized graph data. |
| [`ginuerzh/gost`](https://github.com/ginuerzh/gost) | 18279 | active | GO Simple Tunnel - a simple tunnel written in golang |
| [`snail007/goproxy`](https://github.com/snail007/goproxy) | 17158 | active | 🔥  Proxy is a high performance HTTP(S) proxies, SOCKS5 proxies,WEBSOCKET, TCP, UDP proxy server implemented by golang. Now, it supports chain-style proxies,nat forwarding |
| [`cryptomator/cryptomator`](https://github.com/cryptomator/cryptomator) | 16562 | active | Cryptomator for Windows, macOS, and Linux: Secure client-side encryption for your cloud storage, ensuring privacy and control over your data. |
| [`javascript-obfuscator/javascript-obfuscator`](https://github.com/javascript-obfuscator/javascript-obfuscator) | 16342 | active | A powerful obfuscator for JavaScript and Node.js |
| [`gojue/ecapture`](https://github.com/gojue/ecapture) | 15579 | active | Capture SSL/TLS plaintext with eBPF-no MITM proxy or custom CA installation. Supports Linux and Android on x86_64\arm64 and loong64. |
| [`txthinking/brook`](https://github.com/txthinking/brook) | 15199 | active | A cross-platform programmable network tool |
| [`zama-ai/bounty-program`](https://github.com/zama-ai/bounty-program) | 15194 | archived | Zama Bounty Program: Contribute to the FHE space and Zama's open source libraries and get rewarded 💰 |
| [`cert-manager/cert-manager`](https://github.com/cert-manager/cert-manager) | 14166 | active | Automatically provision and manage TLS certificates in Kubernetes |
| [`jedisct1/libsodium`](https://github.com/jedisct1/libsodium) | 14069 | active | A modern, portable, easy to use crypto library. |
| [`SoftEtherVPN/SoftEtherVPN`](https://github.com/SoftEtherVPN/SoftEtherVPN) | 13778 | active | Cross-platform multi-protocol VPN software. Pull requests are welcome. The stable version is available at https://github.com/SoftEtherVPN/SoftEtherVPN_Stable. |
| [`tink-crypto/tink`](https://github.com/tink-crypto/tink) | 13530 | archived | Tink is a multi-language, cross-platform, open source library that provides cryptographic APIs that are secure, easy to use correctly, and hard(er) to misuse. |
| [`alibaba/tengine`](https://github.com/alibaba/tengine) | 13432 | active | A high-performance web server and reverse proxy, 100% compatible with nginx. |
| [`halfrost/Halfrost-Field`](https://github.com/halfrost/Halfrost-Field) | 13261 | active | ✍🏻 Source Code Deep Dives, System Design & Engineering Blogs / Halfrost-Field 冰霜之地：源码解析、系统设计与工程实践笔记  |

## data

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`swisskyrepo/PayloadsAllTheThings`](https://github.com/swisskyrepo/PayloadsAllTheThings) | 82852 | active | A list of useful payloads and bypass for Web Application Security and Pentest/CTF |
| [`LasCC/HackTools`](https://github.com/LasCC/HackTools) | 7127 | active | The all-in-one browser extension for offensive security professionals 🛠 |
| [`daffainfo/AllAboutBugBounty`](https://github.com/daffainfo/AllAboutBugBounty) | 6979 | active | All about bug bounty (bypasses, payloads, and etc) |
| [`EdOverflow/bugbounty-cheatsheet`](https://github.com/EdOverflow/bugbounty-cheatsheet) | 6598 | active | A list of interesting payloads, tips and tricks for bug bounty hunters. |
| [`hak5/usbrubberducky-payloads`](https://github.com/hak5/usbrubberducky-payloads) | 6196 | active | The Official USB Rubber Ducky Payload Repository |
| [`foospidy/payloads`](https://github.com/foospidy/payloads) | 4005 | active | Git All the Payloads! A collection of web attack payloads. |
| [`1N3/IntruderPayloads`](https://github.com/1N3/IntruderPayloads) | 4000 | active | A collection of Burpsuite Intruder payloads, BurpBounty payloads, fuzz lists, malicious file uploads and web pentesting methodologies and checklists. |
| [`six2dez/OneListForAll`](https://github.com/six2dez/OneListForAll) | 3298 | active | Rockyou for web fuzzing |
| [`swisskyrepo/InternalAllTheThings`](https://github.com/swisskyrepo/InternalAllTheThings) | 2502 | active | Active Directory and Internal Pentest Cheatsheets |
| [`kkrypt0nn/wordlists`](https://github.com/kkrypt0nn/wordlists) | 2429 | active | 📜 Yet another collection of wordlists |
| [`terjanq/Tiny-XSS-Payloads`](https://github.com/terjanq/Tiny-XSS-Payloads) | 2398 | active | A collection of tiny XSS Payloads that can be used in different contexts. https://tinyxss.terjanq.me |
| [`kgretzky/pwndrop`](https://github.com/kgretzky/pwndrop) | 2288 | active | Self-deployable file hosting service for red teamers, allowing to easily upload and share payloads over HTTP and WebDAV. |
| [`nettitude/PoshC2`](https://github.com/nettitude/PoshC2) | 2143 | active | A proxy aware C2 framework used to aid red teamers with post-exploitation and lateral movement. |
| [`insightglacier/Dictionary-Of-Pentesting`](https://github.com/insightglacier/Dictionary-Of-Pentesting) | 2089 | active | Dictionary collection project such as Pentesing, Fuzzing, Bruteforce and BugBounty. 渗透测试、SRC漏洞挖掘、爆破、Fuzzing等字典收集项目。 |
| [`0xSobky/HackVault`](https://github.com/0xSobky/HackVault) | 2037 | active | A container repository for my public web hacks! |
| [`trickest/wordlists`](https://github.com/trickest/wordlists) | 1815 | active | Real-world infosec wordlists, updated regularly |
| [`assetnote/wordlists`](https://github.com/assetnote/wordlists) | 1755 | active | Automated & Manual Wordlists provided by Assetnote |
| [`whwlsfb/BurpCrypto`](https://github.com/whwlsfb/BurpCrypto) | 1652 | active | BurpCrypto is a collection of burpsuite encryption plug-ins, support AES/RSA/DES/ExecJs(execute JS encryption code in burpsuite). 支持多种加密算法或直接执行JS代码的用于爆破前端加密的BurpSuite插件 |
| [`nccgroup/Winpayloads`](https://github.com/nccgroup/Winpayloads) | 1629 | active | Undetectable Windows Payload Generation |
| [`tokyoneon/Chimera`](https://github.com/tokyoneon/Chimera) | 1607 | active | Chimera is a PowerShell obfuscation script designed to bypass AMSI and commercial antivirus solutions. |

## fuzzing

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`Hack-with-Github/Awesome-Hacking`](https://github.com/Hack-with-Github/Awesome-Hacking) | 126201 | active | A collection of various awesome lists for hackers, pentesters and security researchers |
| [`danielmiessler/SecLists`](https://github.com/danielmiessler/SecLists) | 74880 | active | SecLists is the security tester's companion. It's a collection of multiple types of lists used during security assessments, collected in one place. List types include use |
| [`ffuf/ffuf`](https://github.com/ffuf/ffuf) | 17099 | active | Fast web fuzzer written in Go |
| [`maurosoria/dirsearch`](https://github.com/maurosoria/dirsearch) | 15108 | active | Web path scanner |
| [`google/oss-fuzz`](https://github.com/google/oss-fuzz) | 12978 | active | OSS-Fuzz - continuous fuzzing for open source software. |
| [`foundry-rs/foundry`](https://github.com/foundry-rs/foundry) | 10771 | active | Foundry is a blazing fast, portable and modular toolkit for Ethereum application development written in Rust. |
| [`HypothesisWorks/hypothesis`](https://github.com/HypothesisWorks/hypothesis) | 9208 | active | The property-based testing library for Python |
| [`spacejam/sled`](https://github.com/spacejam/sled) | 9123 | active | the champagne of beta embedded databases |
| [`TheKingOfDuck/fuzzDicts`](https://github.com/TheKingOfDuck/fuzzDicts) | 8448 | active | You Know, For WEB Fuzzing !  |
| [`AFLplusplus/AFLplusplus`](https://github.com/AFLplusplus/AFLplusplus) | 6899 | active | AFL++ is a state-of-the-art fuzzer, and #1 in benchmarks. It was originally based on AFL. Today it comes with qemu 5.1, collision-free coverage, enhanced laf-intel & redq |
| [`xmendez/wfuzz`](https://github.com/xmendez/wfuzz) | 6607 | active | Web application fuzzer |
| [`google/syzkaller`](https://github.com/google/syzkaller) | 6420 | active | syzkaller is an unsupervised coverage-guided kernel fuzzer |
| [`GhostTroops/scan4all`](https://github.com/GhostTroops/scan4all) | 6207 | active | Official repository  vuls Scan: 15000+PoCs; 23 kinds of application password crack; 7000+Web fingerprints; 146 protocols and 90000+ rules Port scanning; Fuzz, HW, awesome |
| [`secfigo/Awesome-Fuzzing`](https://github.com/secfigo/Awesome-Fuzzing) | 5955 | active | A curated list of fuzzing resources ( Books, courses - free and paid, videos, tools, tutorials and vulnerable applications to practice on ) for learning Fuzzing and initi |
| [`elceef/dnstwist`](https://github.com/elceef/dnstwist) | 5784 | active | Domain name permutation engine for detecting homograph phishing attacks, typo squatting, and brand impersonation |
| [`google/clusterfuzz`](https://github.com/google/clusterfuzz) | 5638 | active | Scalable fuzzing infrastructure. |
| [`dubzzz/fast-check`](https://github.com/dubzzz/fast-check) | 5296 | active | Property based testing framework for JavaScript (like QuickCheck) written in TypeScript |
| [`dvyukov/go-fuzz`](https://github.com/dvyukov/go-fuzz) | 4844 | active | Randomized testing for Go |
| [`google/AFL`](https://github.com/google/AFL) | 4203 | archived | american fuzzy lop - a security-oriented fuzzer |
| [`evyatarmeged/Raccoon`](https://github.com/evyatarmeged/Raccoon) | 4078 | active | A high performance offensive security tool for reconnaissance and vulnerability scanning |

## burp

| Repo | Stars | Status | What it does |
|---|---:|---|---|
| [`aress31/burpgpt`](https://github.com/aress31/burpgpt) | 2399 | active | A Burp Suite extension that integrates OpenAI's GPT to perform an additional passive scan for discovering highly bespoke vulnerabilities and enables running traffic-based |
| [`PortSwigger/turbo-intruder`](https://github.com/PortSwigger/turbo-intruder) | 1825 | active | Turbo Intruder is a Burp Suite extension for sending large numbers of HTTP requests and analyzing the results. |
| [`doyensec/inql`](https://github.com/doyensec/inql) | 1815 | active | InQL is a robust, open-source Burp Suite extension for advanced GraphQL testing, offering intuitive vulnerability detection, customizable scans, and seamless Burp integra |
| [`wagiro/BurpBounty`](https://github.com/wagiro/BurpBounty) | 1813 | active | Burp Bounty (Scan Check Builder in BApp Store) is a extension of Burp Suite that allows you, in a quick and simple way, to improve the active and passive scanner by means |
| [`sting8k/BurpSuite_403Bypasser`](https://github.com/sting8k/BurpSuite_403Bypasser) | 1712 | active | Burpsuite Extension to bypass 403 restricted directory |
| [`summitt/Nope-Proxy`](https://github.com/summitt/Nope-Proxy) | 1661 | active | TCP/UDP Non-HTTP Proxy Extension (NoPE)  for  Burp Suite. |
| [`six2dez/burp-ai-agent`](https://github.com/six2dez/burp-ai-agent) | 1595 | active | Burp Suite extension that adds built-in MCP tooling, AI-assisted analysis, privacy controls, passive and active scanning and more |
| [`Quitten/Autorize`](https://github.com/Quitten/Autorize) | 1173 | active | Automatic authorization enforcement detection extension for burp suite written in Jython developed by Barak Tawily in order to ease application security people work and a |
| [`xl7dev/BurpSuite`](https://github.com/xl7dev/BurpSuite) | 999 | active | BurpSuite using the document and some extensions |
| [`hisxo/ReconAIzer`](https://github.com/hisxo/ReconAIzer) | 903 | active | A Burp Suite extension to add OpenAI (GPT) on Burp and help you with your Bug Bounty recon to discover endpoints, params, URLs, subdomains and more! |
| [`RhinoSecurityLabs/IPRotate_Burp_Extension`](https://github.com/RhinoSecurityLabs/IPRotate_Burp_Extension) | 897 | active | Extension for Burp Suite which uses AWS API Gateway to rotate your IP on every request. |
| [`praetorian-inc/titus`](https://github.com/praetorian-inc/titus) | 777 | active | High-performance secrets scanner. CLI, Go library, Burp Suite extension, and Chrome extension. 487 detection rules with live credential validation. |
| [`nccgroup/BurpSuiteHTTPSmuggler`](https://github.com/nccgroup/BurpSuiteHTTPSmuggler) | 745 | active | A Burp Suite extension to help pentesters to bypass WAFs or test their effectiveness using a number of techniques |
| [`nccgroup/LoggerPlusPlus`](https://github.com/nccgroup/LoggerPlusPlus) | 711 | active | Advanced Burp Suite Logging Extension |
| [`bit4woo/domain_hunter`](https://github.com/bit4woo/domain_hunter) | 673 | active | A Burp Suite Extension that try to find all sub-domain, similar-domain and related-domain of an organization automatically! 基于流量自动收集整个企业或组织的子域名、相似域名、相关域名的burp插件 |
| [`SecurityInnovation/AuthMatrix`](https://github.com/SecurityInnovation/AuthMatrix) | 648 | active | AuthMatrix is a Burp Suite extension that provides a simple way to test authorization in web applications and web services. |
| [`WuliRuler/AutorizePro`](https://github.com/WuliRuler/AutorizePro) | 629 | active | 🧿 AutorizePro是一款强大越权检测 Burp 插件，通过增加 AI 辅助分析 && 进一步优化检测逻辑，大幅降低误报率，提升越权漏洞检出效率。    [ AutorizePro is a authorization enforcement detection extension for burp suite. By  addin |
| [`PortSwigger/bypass-bot-detection`](https://github.com/PortSwigger/bypass-bot-detection) | 506 | active | Burp Suite extension that mutates ciphers to bypass TLS-fingerprint based bot detection |
| [`silentchainai/SILENTCHAIN`](https://github.com/silentchainai/SILENTCHAIN) | 469 | active | AI-powered vulnerability scanner extension for Burp Suite with multi-provider support (Ollama, OpenAI, Claude, Gemini) |
| [`PortSwigger/collaborator-everywhere`](https://github.com/PortSwigger/collaborator-everywhere) | 455 | active | A Burp Suite Pro extension which augments your proxy traffic by injecting non-invasive headers designed to reveal backend systems by causing pingbacks to Burp Collaborato |

## Search cookbook

GitHub search is the fastest way to extend this atlas.

```text
topic:bug-bounty pushed:>2025-01-01 stars:>50
topic:ghidra language:java
topic:detection-engineering sigma
"nuclei-templates" topic:cve
topic:wordlists stars:>100
topic:llm-security prompt
```

Useful qualifiers: `language:`, `stars:`, `pushed:`, `archived:false`, `license:`, `topic:`.

## Limits

- Stars favor old and broad projects. A new narrow tool can be better.
- Topics are user-chosen and noisy. A security-adjacent project can appear in a security topic.
- Vendored skill bundles inflate counts.
- The atlas is not a dependency audit. Check the license and the maintainer.
- A repository is not a finding. Run the tool and reproduce the result.

## Sources

- GitHub search API and topic pages
- `scripts/github_sweep.py` for the reproducible query set
- `data/github_atlas.jsonl` for the raw rows