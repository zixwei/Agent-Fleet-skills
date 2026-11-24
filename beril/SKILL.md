---
name: beril
description: Build and refresh a cybersecurity intelligence base with Camoufox. Harvests anti-bot forums, disclosed reports, researcher blogs and GitHub tool shelves; profiles experts; extracts their methods; writes an evidence-linked dossier; and runs a read-only scan of an app you own. Use for bug bounty recon, pentest planning, threat research, expert tracking, tool selection, application security checks, or when a security site blocks ordinary scraping. Turkish triggers: siber güvenlik araştırması, forum toplama, uzman profili, tool kataloğu, metod çıkarma, bug bounty keşif, uygulama taraması.
---

# Beril

Beril is a research loop for offensive security intelligence. It uses Camoufox, a patched Firefox with Playwright, to read sites that block plain HTTP clients. It stores what it finds as four linked catalogs: forums and threads, experts, tools, and methods. The output is a dossier you can defend line by line because every entry has a public URL.

Use it when you need current field knowledge rather than a static list. Examples: choosing recon tools for a target, learning how top hunters approach a vulnerability class, tracking a researcher's recent work, or building a reading queue before an engagement.

## When to use

- A bounty program starts and you want the current tools and methods for its stack.
- A security site returns 403, a Cloudflare challenge, or an empty JavaScript shell to curl.
- You need to turn scattered forum posts and disclosures into a ranked reading list.
- You want to compare experts by what they publish, not by follower count.
- You are preparing a pentest and need a phase-by-phase tool chain with install commands.
- A previous dossier is older than 30 days and the stars, reports, or threads have moved.

Skip Beril when a single page answers the question. Open the page, read it, and leave the catalog alone.

## Install

```bash
python -m venv .venv
source .venv/bin/activate
pip install -U "camoufox[geoip]" beautifulsoup4 lxml
camoufox fetch
```

`camoufox fetch` downloads the browser and GeoIP database. It needs about 300 MB. On a headless Linux box, install `xvfb` if you want the headful fallback for challenge pages.

Run everything from the skill directory:

```bash
cd skills/beril
python scripts/validate.py
```

## One-click scan

For a first pass on an application or artifact you own:

```bash
./install.sh
./beril scan --target https://your-app.example
./beril scan --target ./build/app.apk
./beril scan --target ./source --tools
./beril scan --target ./release/app.exe
./beril scan --target ./sample --deep
./beril re --target ./sample --capa --floss --rizin
```

The scanner is read-only and static for local files. Web scans report headers, TLS, cookies, exposed paths and fingerprints. Artifact scans read manifests, hardening flags, secrets, dangerous sinks, and archive paths. Add nuclei for a known-CVE pass on a running app. Walkthroughs are in `docs/one-click.md` and `docs/scan-artifacts.md`.

## Ten-minute run

```bash
# 1. Pull the current public surface
python scripts/harvest.py forums --sources 0x00sec,htb,reddit,hackerone --limit 25
python scripts/harvest.py experts --sources curated,hackerone,portswigger --limit 40
python scripts/harvest.py tools --sources canonical --limit 60
python scripts/harvest.py methods --limit 20

# 2. Turn the best URLs into notes with headings and code density
python scripts/extract.py --input data/threads.jsonl --out data/thread_details.jsonl --limit 25

# 3. Build the reading dossier
python scripts/build_dossier.py --data data --out examples/dossier.md

# 4. Check schema and prose
python scripts/validate.py
```

All commands accept `--headful`, `--delay`, `--cache`, and `--fresh`. The cache is keyed by URL hash and lives in `raw/`.

## The loop

### 1. Scope the question

Write one sentence before touching a browser: "I need the current external recon method for GraphQL APIs on bug bounty programs." A vague goal produces a 400-row dump nobody reads.

Then pick the layers that can answer it:

| Question | Layers | First sources |
|---|---|---|
| What is being found now? | Threads, reports | HackerOne Hacktivity, r/bugbounty, 0x00sec |
| Who knows this best? | Experts | PortSwigger authors, Intigriti spotlights, report authors |
| What should I run? | Tools | ProjectDiscovery, GitHub topics, canonical shelf |
| How do they do it? | Methods | Researcher blogs, conference talks, docs |

Two layers are enough for a focused brief. Use all four for a new target or a quarterly refresh.

### 2. Collect with Camoufox

Use the harvesters first. They know the site quirks: Discourse JSON, Ghost RSS, Reddit shadow DOM, HackerOne's disclosure grid.

```bash
python scripts/harvest.py forums --sources hackerone --limit 50
python scripts/harvest.py experts --sources hackerone --limit 60
```

The browser is shared across a run. Keep concurrency low. One request every 1 to 2 seconds per host is polite and usually enough to avoid a challenge.

### 3. Extract, do not summarize yet

`extract.py` records structure: title, headings, intro, code block count, table count. Structure tells you whether a page contains a reproducible method or only commentary. A page with 12 headings and 40 code blocks is worth reading. A page with one heading and no code is a news item.

```bash
python scripts/extract.py --input data/methods.jsonl --out data/method_details.jsonl --limit 30
```

Read the top 10 notes yourself. Beril cannot tell whether a technique is correct; it can only show you where the evidence is.

### 4. Verify the claim

For every method you keep, answer four questions in the note:

1. What is the precondition? Version, configuration, role, feature flag.
2. What is the smallest request or command that shows the behavior?
3. What is the observable proof? Response diff, callback, log line, screenshot.
4. What makes it fail? WAF, parser choice, cache, patched version.

If the source does not answer them, mark the gap. Do not fill it with plausible detail.

### 5. Write the dossier

`build_dossier.py` gives you coverage counts, ranked threads, expert reading, a tool shelf, and a method index. Add a short "what changed" section by hand. State the date, the sources you actually reached, and what you could not reach.

### 6. Refresh

Monthly is enough for tools and experts. Weekly is better for disclosed reports and forum threads. Store each snapshot with its date. When a star count or report page changes, the old dossier still records what you saw.

## Source board

| Source | Access | Take | Watch out |
|---|---|---|---|
| 0x00sec | Ghost RSS and tag pages | Low-level exploitation, reverse engineering, red team | Editorial account posts most items; read the byline in the body |
| Hack The Box Forum | Discourse JSON | Lab questions, certification talk, beginner paths | Many threads are single-question homework |
| Reddit r/netsec, r/bugbounty | `sh.reddit.com`, shadow DOM | Field reports, disclosure drama, methodology threads | Comment text bleeds into post text; trim before quoting |
| HackerOne Hacktivity | Public disclosure grid | Real report titles, weakness, severity, bounty, author | Report bodies often need login; the grid metadata is public |
| Intigriti researchers blog | Public listing and RSS | Writeups, hacker interviews, tool reviews | Category pages mix news with technique |
| PortSwigger Research | Author pages and RSS | Web exploitation primitives, desync, XSS, cache | Listing includes topic hubs; filter to article URLs |
| ProjectDiscovery blog | Public listing | Recon tooling, nuclei templates, AI pentest benchmarks | Product posts outnumber method posts |
| YesWeHack blog | Public categories | Bug bounty lessons, Dojo challenges | Some articles are program news |
| Bugcrowd VRT | Public taxonomy | Severity language and report classification | Not a forum |
| DEF CON media | Public directory index | Talks and slides by year | Large files; index only unless you need the talk |
| GitHub topics | Public HTML | Tool discovery and rough popularity | Topic tags are noisy; verify the repo does security work |
| Hacker News, Lobsters | Public | Practitioner discussion, postmortems | Signal is uneven; use as a lead generator |

More detail, including endpoints and query strings, is in `references/forums.md`.

## Entity rules

Keep the same shape across runs. The scripts already write these fields.

### Thread or report

```json
{"type":"thread","source":"HackerOne Hacktivity","url":"https://hackerone.com/reports/...",
 "title":"...","author":"handle","date":"","tags":["disclosed"],
 "summary":"...","metrics":{"program":"...","weakness":"...","severity":"High","bounty":"$2,000"},
 "collected_at":"2026-11-22T00:00:00+00:00"}
```

A thread is any public discussion unit: forum post, disclosed report, blog article with comments. Do not store comment trees unless the comment adds a reproduction detail.

### Expert

```json
{"type":"expert","source":"PortSwigger Research","name":"James Kettle",
 "handle":"albinowax","profile":"https://portswigger.net/research/james-kettle",
 "focus":"request smuggling, cache poisoning, desync",
 "works":[{"title":"HTTP/2: The Sequel is Always Worse","url":"..."}],
 "collected_at":"..."}
```

`focus` describes what the person publishes. Do not invent a biography, employer, or country. If the only evidence is a report handle, write that.

### Tool

```json
{"type":"tool","source":"canonical","category":"recon","repo":"https://github.com/projectdiscovery/subfinder",
 "name":"projectdiscovery/subfinder","description":"Fast passive subdomain enumeration tool.",
 "stars":"14845","language":"Go","install":["go install ..."],"flags":[],"collected_at":"..."}
```

Star counts are a snapshot. Label them with the collection date in any prose. A repo with no README or a last release from years ago is still useful, but say so.

### Method

```json
{"type":"method","source":"PortSwigger Research","url":"...","title":"...",
 "headings":["..."],"intro":"...","code_blocks":42,"tables":3,"collected_at":"..."}
```

A method note is not a summary of the article. It is the transferable procedure plus the evidence that it worked.

## Camoufox rules

The full playbook is in `references/camoufox-playbook.md`. The short version:

- Keep the fingerprint consistent. One locale, one timezone, one OS, one browser version per session.
- Start headless with `humanize=True`. Move to headful only when a challenge persists.
- Warm the origin before fetching an RSS or JSON endpoint from page context.
- Prefer the site's JSON or RSS endpoint over the rendered page.
- Wait for a selector, not for `networkidle`. Security sites keep analytics and websockets open.
- Cache every response. Re-parsing should not require a second request.
- Back off on 403, 429, and 503. Three attempts with growing delays is enough.
- Do not solve CAPTCHAs with paid services unless the engagement allows it.
- One identity per session. Rotating proxies and user agents inside one run creates more suspicion than it removes.

For shadow DOM sites such as Reddit, read the host element with `inner_text`. BeautifulSoup sees the light DOM only.

## Writing rules

The dossier is read by a human under time pressure. Apply the humanizer rules while writing:

- State the fact. Cut filler such as "it is important to note".
- No em dashes or en dashes. Use a period, comma, colon, or parentheses.
- Avoid the "X, but Y" contrast unless the negative half corrects a real belief.
- No triad for rhythm. Two items or four are fine.
- Sentence case headings. No decorative emoji.
- Straight quotes. No curly quotes.
- Every technique claim links to the source that showed it.
- Separate observation from inference: "The report shows X. This suggests Y, which I did not test."
- Short paragraphs. A 40-word paragraph beats a 120-word wall when the reader is scanning for a command.

Run the humanizer skill on long narrative sections when it is available. Tables and command blocks do not need a style pass.

## Quality gates

Do not publish a dossier until all of these pass.

| Gate | Test |
|---|---|
| Traceability | Every row has a public URL and a collection timestamp |
| Coverage | At least three source types across the four layers |
| Duplicates | Under 10 percent duplicate URLs after the merge |
| Freshness | Reports and threads from the last 12 months dominate |
| Evidence | Every method card names a precondition and a proof |
| Safety | No credentials, tokens, private reports, or personal data |
| Reproducibility | Every command in the dossier was run in this environment |
| Prose | `validate.py` reports zero em dashes and no schema errors |

Use `python scripts/validate.py --links --max-links 40` before publishing. A dead link is acceptable if you note it. A dead link presented as live is not.

## Outputs

- `examples/dossier.md` is the human brief.
- `data/*.jsonl` is the machine catalog.
- `raw/` is the response cache and the audit trail.
- `evidence/` holds screenshots and DOM snapshots for claims that may change.

Keep the dossier and the JSONL in sync. If you edit a fact by hand, edit the row that produced it or add a note explaining the override.

## Commands

```bash
# Harvest one layer
python scripts/harvest.py forums --sources 0x00sec,htb,reddit,hackerone,intigriti
python scripts/harvest.py experts --sources curated,hackerone,portswigger,intigriti
python scripts/harvest.py tools --sources canonical,topics --limit 80
python scripts/harvest.py methods --limit 25

# Harvest everything into data/
python scripts/harvest.py all --out-dir data --limit 30

# Deep-read the best URLs
python scripts/extract.py --input data/methods.jsonl --out data/method_details.jsonl --limit 30
python scripts/extract.py --url https://0x00sec.org/some-post/ --out /tmp/note.json

# Assemble and check
python scripts/build_dossier.py --data data --out examples/dossier.md
python scripts/validate.py

# Read-only application and artifact scan
./beril scan --target https://your-app.example --out scans/myapp
./beril scan --target ./build/app.apk --out scans/app
./beril scan --target ./source --tools --out scans/source
./beril scan --target ./bin/service --out scans/service

# Training and labs
./beril train --status
./beril train --week 1
./beril labs --run web

# Deep binary triage
./beril re --target ./bin/service --capa --floss --rizin --out scans/service-re
GHIDRA_HOME=~/tools/ghidra ./scripts/ghidra_headless.sh ./bin/service scans/service.ghidra.json
```

## Reference map

| File | Use |
|---|---|
| `references/camoufox-playbook.md` | Browser settings, challenge ladder, site recipes, failure modes |
| `references/forums.md` | Community catalog, endpoints, what to mine, etiquette |
| `references/experts.md` | Researcher profiles and signature methods |
| `references/tooling.md` | Phase map, install commands, tool chains |
| `references/techniques.md` | Method cards for web, API, cloud, AD, mobile, binary, AI |
| `references/bug-method-matrix.md` | 100+ tests by class with proof |
| `references/source-catalog.md` | Every feed, API, archive and upstream skill repo |
| `references/dossier-template.md` | Output structure and section prompts |
| `references/scoring.md` | Ranking and keep/drop rules |
| `docs/one-click.md` | Install, scan and troubleshoot without the terminal |
| `docs/scan-artifacts.md` | APK, IPA, PE, ELF, archive and source review |
| `docs/reverse-engineering.md` | Deep binary workflow and optional tool install |
| `references/reverse-engineering.md` | Ghidra, Binary Ninja, IDA, rizin, debuggers, patch diff |
| `references/vulnerability-research.md` | Bug classes, source audit, crash triage, exploitability |
| `references/fuzzing.md` | Harnesses, AFL++, sanitizers, corpora, CI |
| `references/exploit-development.md` | ROP, heap, UAF, Windows and kernel |
| `references/mobile-reverse.md` | Android and iOS static and runtime work |
| `references/firmware-iot.md` | Extraction, emulation, UART, JTAG, radio |
| `references/ghidra-ecosystem.md` | GhidRust, Ghidrust, plugin safety and high-signal plugins |
| `references/rust-reverse.md` | Rust layout, symbols, monomorphization and tooling |
| `references/re-plugin-index.md` | Generated plugin and tool index by ecosystem |
| `references/github-atlas.md` | 2,064 security repositories by topic |
| `references/claude-bughunter.md` | 83-skill bounty bundle, 7-Question Gate, evidence hygiene |
| `references/artex.md` | Autonomous multi-agent pentest platform |
| `references/curriculum.md` | 25-week training plan and grading |
| `references/exercises.md` | 68 drills by domain |
| `references/self-training.md` | Turn labs into durable method notes |
| `references/books.md` | Reading canon by domain |
| `docs/mastery.md` | Long-term specialist tracks |
| `docs/training.md` | How to run the training loop |
| `docs/agents.md` | Entry files for Claude, Cursor, Gemini, Copilot and others |
| `docs/dashboard.md` | Local tracking dashboard and its limits |
| `agents/` | Forum, expert, tool, method, RE, crash, fuzzing and firmware prompts |

## Legal and ethical bounds

Collect only public pages. Respect robots.txt, terms of service, and rate limits. Do not bypass a login to reach a report, and do not scrape private program data. Quote short excerpts with a link; do not republish whole articles. Keep personal data out of the catalog unless the person published it for professional use. When in doubt, link and paraphrase.

Testing techniques from the catalog against a target still requires authorization. A method card is not a license.
