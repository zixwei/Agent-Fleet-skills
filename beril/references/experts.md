# Expert catalog

The catalog answers a narrow question: who publishes useful offensive security work, and what is their signature method? It is not a ranking of skill. A person with three deep writeups can be more useful than an account with a large following.

Entries fall into three groups:

- Named researchers with a public body of work.
- Rising handles from recent HackerOne disclosures. Their focus is inferred from report titles, so treat it as a lead.
- Teams and labs that publish under an organization name.

Every profile links to a primary page. If a link dies, search the name plus the work title.

## How to use the list

1. Match the target to a focus area, not to fame.
2. Read one primary work before quoting the person.
3. Check the date. A 2019 desync primitive may still work, but the parsers have changed.
4. Look for the method, not the payload. Payloads age faster than the reasoning.
5. When you cite a talk or post, link the exact URL.

## Main table

| Name | Handle | Focus | Read |
|---|---|---|---|
| James Kettle | albinowax | HTTP desync, request smuggling, cache poisoning, research automation | [PortSwigger author page](https://portswigger.net/research/james-kettle) |
| Gareth Heyes | garethheyes | XSS, parser differentials, CSP bypass, client-side exfiltration | [PortSwigger author page](https://portswigger.net/research/gareth-heyes) |
| Zakhar Fedotkin | zakhar-fedotkin | SAML, cookie prefixes, WebSocket testing, client-side parser tricks | [PortSwigger author page](https://portswigger.net/research/zakhar-fedotkin) |
| Tom Stacey | tom-stacey | CRLF desync, header injection, HTTP/3 tooling | [PortSwigger author page](https://portswigger.net/research/tom-stacey) |
| Alex Craig | alex-craig | Web research, Burp tooling | [PortSwigger author page](https://portswigger.net/research/alex-craig) |
| Ben Sadeghipour | nahamsec | Recon, live hacking, content discovery, bounty strategy | [nahamsec.com](https://www.nahamsec.com/) |
| Jason Haddix | Jhaddix | Bug hunter methodology, attack surface, recon at scale | [X profile](https://x.com/Jhaddix) |
| Tom Hudson | tomnomnom | Recon tooling, Go, composable command-line workflows | [tomnomnom.com](https://tomnomnom.com/) |
| Frans Rosen | fransrosen | Account takeover chains, postMessage, OAuth, web logic | [fransrosen.com](https://fransrosen.com/) |
| STOK | stokfredrik | Content discovery, recon process, live hacking | [X profile](https://x.com/stokfredrik) |
| EdOverflow | edoverflow | OAuth, disclosure, web security | [edoverflow.com](https://edoverflow.com/) |
| Mazin Ahmed | mazen160 | SSRF, application security, recon | [mazinahmed.net](https://mazinahmed.net/) |
| Sam Curry | samwcurry | Web exploitation, supply chain, embedded systems | [samcurry.net](https://samcurry.net/) |
| Orange Tsai | orange_8361 | Parser bugs, SSRF, network appliances, web exploitation | [blog.orange.tw](https://blog.orange.tw/) |
| Justin Gardner | Rhynorater | Web exploitation, automation, bounty workflow | [X profile](https://x.com/Rhynorater) |
| Luke Stephens | hakluke | Recon automation, tooling, bounty process | [hakluke.com](https://hakluke.com/) |
| Zhero | zhero___ | API and web exploitation, bounty workflow | [X profile](https://x.com/zhero___) |
| Ryan Black | ryancblack | Red team, cloud, Active Directory | [X profile](https://x.com/ryancblack) |
| Sha | 0xsha | Windows internals, exploit development | [X profile](https://x.com/0xsha) |
| Omar Santos | santosomar | Threat intelligence, hacking corpus, security research | [h4cker repo](https://github.com/The-Art-of-Hacking/h4cker) |
| fg0x0 | fg0x0 | C connector memory bugs, ODBC, fuzzing | [HackerOne profile](https://hackerone.com/fg0x0) |
| sumit-sabhaya | sumit-sabhaya | XML entity expansion, VCS metadata exposure, web parsing | [HackerOne profile](https://hackerone.com/sumit-sabhaya) |
| 0xbs0d27 | 0xbs0d27 | WordPress logic bugs, access control | [HackerOne profile](https://hackerone.com/0xbs0d27) |
| jakubk | jakubk | Nextcloud privilege escalation, token handling | [HackerOne profile](https://hackerone.com/jakubk) |
| matty69v | matty69v | Local file read, cross-account XSS | [HackerOne profile](https://hackerone.com/matty69v) |
| deivizzzz | deivizzzz | Webhook takeover, OAuth token minting | [HackerOne profile](https://hackerone.com/deivizzzz) |
| stze | stze | TLS certificate validation, OpenSSL and wolfSSL | [HackerOne profile](https://hackerone.com/stze) |
| mschorm | mschorm | MySQL Connector TLS and memory safety | [HackerOne profile](https://hackerone.com/mschorm) |
| sujitjaunjal | sujitjaunjal | Certificate trust, cross-protocol redirects | [HackerOne profile](https://hackerone.com/sujitjaunjal) |
| Ryan Bonner | Roll4CombatUS | Bounty process, hacker spotlight | [Intigriti interview](https://www.intigriti.com/researchers/blog/hacker-spotlight/interview-with-ryan-bonner-aka-roll4combatus) |
| Cristian Zot | CristiVlad25 | AI-assisted bounty work, hacker spotlight | [Intigriti interview](https://www.intigriti.com/researchers/blog/hacker-spotlight/using-ai-the-smart-way-interview-with-hacker-cristian-zot-cristivlad25) |

## Signature methods

### James Kettle: find the parser disagreement

Kettle's work starts with a question about how two components read the same bytes. Request smuggling is the canonical case: a front end and a back end disagree about where a request ends. His method generalizes to cache poisoning, HTTP/2 downgrades, and browser-powered desync.

The procedure:

1. Identify every component that parses the request: CDN, load balancer, reverse proxy, app server, framework.
2. Find a byte sequence that one parser treats as a delimiter and another treats as data. `Content-Length` versus `Transfer-Encoding`, bare LF versus CRLF, header folding, HTTP/2 pseudo-headers.
3. Send a request that makes the disagreement visible. A timing difference, a poisoned cache entry, or a response for the wrong user.
4. Prove impact with a second request from a clean client.

Read: "HTTP Desync Attacks: Request Smuggling Reborn", "HTTP/2: The Sequel is Always Worse", "Web Cache Entanglement". His "Backslash Powered Scanning" paper is the method for hunting unknown vulnerability classes rather than known patterns.

### Gareth Heyes: treat the browser as a parser collection

Heyes looks for places where HTML, CSS, URLs, and JavaScript parsers disagree. A sanitizer uses one parser, the browser uses another, and the gap is the bug.

The procedure:

1. Map every parser in the path: sanitizer, templating engine, browser HTML parser, CSS parser, URL parser.
2. Find an input that is inert to the sanitizer and active to the browser. Encoding, comments, mutation XSS, attribute boundaries.
3. Keep the payload small enough to survive truncation and normalization.
4. Prove execution in the target browser, not only in a DOM simulator.

Read: "CSS: the bomb inside your inbox", "Blind CSS Exfiltration", "Achieving the impossible: XSS in innerHTML without equals". The email parser work is a good model for any system that sanitizes one syntax and renders another.

### Zakhar Fedotkin: attack the assertion, not the login form

Fedotkin's SAML work targets the gap between what the identity provider signs and what the service provider reads. XML signature wrapping, attribute pollution, and parser differences can turn a valid assertion into an authentication bypass.

The procedure:

1. Capture a real SAML response for a low-privilege account.
2. Map which XML nodes the signature covers and which nodes the SP trusts.
3. Move or duplicate the signed assertion, then change the unsigned identity fields.
4. Test attribute pollution and namespace confusion.
5. Confirm with a fresh session and the target account.

Read: "The Fragile Lock: Novel Bypasses For SAML Authentication", "Cookie Chaos", "WebSocket Turbo Intruder".

### Tom Stacey: make header injection critical

Stacey's CRLF work turns a small header injection into response queue poisoning. The method is to find a CRLF sink, then use it to split a response or inject a header that changes the next user's response.

The procedure:

1. Find any user input that reaches a response header without sanitization.
2. Confirm you can inject a full header line, not a single value.
3. Use request splitting to make the back end answer a request that the front end did not send.
4. Poison the queue and observe the next response.

Read: "CRLF-Powered Desync Attacks: Beheading HTTP Streams", "Making HTTP header injection critical with one prompt".

### Ben Sadeghipour: recon as a pipeline

NahamSec's public work treats recon as a repeatable pipeline: scope, subdomains, live hosts, screenshots, content discovery, then manual review. The value is in the filtering, not in the tool list.

The procedure:

1. Build the asset list from scope, certificates, DNS, and acquisitions.
2. Resolve and probe for live HTTP services.
3. Screenshot and fingerprint. Group similar hosts.
4. Spend manual time on the hosts that look different or forgotten.
5. Keep a session log so the next pass does not repeat the same checks.

Read his live hacking streams and the nahamsec.com resources. Pair with the recon tool chain in `tooling.md`.

### Jason Haddix: map the attack surface before hunting

Haddix's methodology is built around breadth first, then depth. The goal is to find the asset nobody else is testing: an old acquisition, a staging host, a mobile API, a third-party integration.

The procedure:

1. Enumerate the organization, not only the domain.
2. Collect ASNs, certificates, cloud ranges, mobile apps, and GitHub organizations.
3. Rank assets by business value and by how recently they changed.
4. Test the least-observed asset first.

Read "The Bug Hunter's Methodology" talks and the accompanying tool lists.

### Tom Hudson: small tools, clear data flow

TomNomNom's tools share a design rule: read lines, write lines, do one thing. `waybackurls`, `assetfinder`, `unfurl`, `qsreplace`, and `hakrawler` compose in a shell pipeline.

The method:

1. Get a list of URLs from passive sources.
2. Normalize them with `unfurl` or `qsreplace`.
3. Deduplicate with `anew`.
4. Feed the result to a prober or a parameter tester.
5. Keep each stage inspectable.

Read the READMEs and the source. The tools are short, which makes them good examples of how to build your own filters.

### Frans Rosen: follow the token

Frans Rosen's account takeover work follows authentication tokens across domains and services. The bug is usually a token that is trusted in a context it was not issued for.

The procedure:

1. Map every place a token is issued, stored, and accepted.
2. Look for weaker validation on a sibling service or an older API.
3. Test cross-domain postMessage handlers and OAuth redirects.
4. Chain a low-impact leak into a session or account takeover.

Read his posts on postMessage, OAuth, and account takeover chains.

### Orange Tsai: study the appliance

Orange Tsai's research often targets network appliances and parsers that receive less scrutiny than web frameworks. The method is to enumerate the appliance's own HTTP stack, management API, and file parsers.

The procedure:

1. Fingerprint the appliance and its firmware version.
2. Extract the management interface and any embedded web server.
3. Test path normalization, proxy behavior, and upload parsers.
4. Chain a parser bug into authentication bypass or command execution.

Read "A New Era of SSRF" and the appliance research on blog.orange.tw.

### fg0x0: fuzz the connector boundary

The recent HackerOne reports from fg0x0 target MySQL Connector/ODBC memory handling. The pattern is a client library that trusts a length or an offset from the server.

The procedure:

1. Find a client library that parses server-controlled lengths.
2. Build a malicious or malformed server response.
3. Watch for out-of-bounds reads and memory disclosure.
4. Prove the leak with a controlled buffer.

Read the reports linked from the profile. They are a good example of protocol-level fuzzing.

### sumit-sabhaya: test metadata and entity expansion

Recent reports cover XML entity expansion and VCS metadata exposure. Both are cases where a build or deployment step leaves a parser or a directory reachable.

The procedure:

1. Look for XML parsers that expand entities.
2. Send a bounded billion-laughs payload and watch memory.
3. Check for `.svn`, `.git`, and other metadata directories in web roots.
4. Prove disclosure with a file that should not be public.

### stze and mschorm: certificate validation is a parser problem

Recent reports from stze and mschorm target TLS certificate validation in OpenSSL, wolfSSL, and MySQL Connector. The common thread is a validation step that accepts a chain the application did not intend.

The procedure:

1. Build a test chain with a name constraint or a native CA that the library mishandles.
2. Present it to the client and observe whether the connection succeeds.
3. Prove the identity mismatch with a certificate the client should reject.
4. Test across versions; these bugs are often fixed in a point release.

## Teams and labs

| Team | What they publish | Where |
|---|---|---|
| PortSwigger Research | Web primitives, desync, XSS, cache, tooling | `https://portswigger.net/research` |
| ProjectDiscovery | Recon tooling, nuclei templates, benchmark studies | `https://projectdiscovery.io/blog` |
| Assetnote / Searchlight Cyber | Attack surface, appliance RCE, fuzzing | `https://www.slcyber.io/research` |
| Trail of Bits | Audits, fuzzing, cryptography, supply chain | `https://github.com/trailofbits` |
| Google Project Zero | Deep vulnerability research and disclosure | `https://googleprojectzero.blogspot.com/` |
| watchTowr Labs | Appliance and edge device research | `https://labs.watchtowr.com/` |
| Horizon3.ai | Exploit analysis and CVE deep dives | `https://horizon3.ai/attack-research/` |
| Rapid7 AttackerKB | Exploitability analysis and community notes | `https://attackerkb.com/` |

## Rising handles from the snapshot

These handles appeared in the 100 most recent disclosed HackerOne reports on the build date. The focus line comes from the report titles, so it is a snapshot, not a career summary.

| Handle | Recent themes |
|---|---|
| audion | Nextcloud permission checks, cache-control behavior |
| laudas | Image parser heap overflow, launchpad file read |
| miauuu | Reflected XSS, campaign ownership checks |
| mlopez01 | Stored XSS, unauthenticated reflected XSS |
| yoyomiski | Nextcloud read-only access, team enumeration |
| 0xbs0d27 | WordPress event permissions, invite token validation |
| matty69v | Local file read, cross-account stored XSS |
| deivizzzz | Webhook takeover, OAuth token scope |
| jakubk | Nextcloud subadmin takeover, CampTix token issues |

Do not treat a handle as a persona. Read the reports before drawing a conclusion.

## Keeping this file current

1. Run `harvest.py experts --sources hackerone,portswigger,intigriti`.
2. Sort by recent work count, not total.
3. Add a person only if you can link a primary page and at least one work.
4. Remove a profile when the primary page is gone and no archive exists.
5. Date the snapshot in the commit message.
