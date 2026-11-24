# Forum and community catalog

The list is grouped by how much work a source needs. Tier 1 sources were fetched during the build of this skill and have tested endpoints. Tier 2 sources are stable public references that you should check before relying on an endpoint. Tier 3 covers places that need a login or a human in the loop.

Every source entry answers four questions: where is it, how do I read it, what do I take, and what will waste my time.

## Tier 1: tested sources

### 0x00sec

- URL: `https://0x00sec.org/`
- Platform: Ghost blog with an older Discourse forum at `https://forum.0x00sec.org/`
- Read it with: RSS at `/rss/`, tag feeds such as `/tag/redteam/rss/` and `/tag/reverse-engineering/rss/`, the deep dive series at `/deep-dive-series/`.
- Take: low-level exploitation, reverse engineering, malware analysis, red team notes. Articles often include full code and a threat model.
- Watch out: the RSS `dc:creator` is usually the editorial account `james`. The real author appears in the first paragraph as "written by" or in a byline. Store the editorial account if you cannot find a person.
- Good queries: `site:0x00sec.org ptrace`, `site:0x00sec.org parser differential`, `site:0x00sec.org PIE format string`.

Example thread titles from the build snapshot:

- "ZIP Parser Differentials and Mutable Encrypted Filenames"
- "LBOFS: Debugging and Anti-debugging fun with ptrace"
- "X86_64 Linux Remote Stack Overflow Exploitation: bypass PIE via format string information leak"

### Hack The Box Forum

- URL: `https://forum.hackthebox.com/`
- Platform: Discourse
- Read it with: `/latest.json`, `/top.json?t=year`, `/top.json?t=all`, `/search.json?q=...`
- Take: machine hints, certification paths, beginner methodology, tool setup questions.
- Watch out: a large share of threads are single questions about an academy exercise. Sort by views or replies and skip anything that looks like homework.
- Useful fields: `posts_count`, `views`, `created_at`, `excerpt`.

### Reddit

- Subreddits: `r/netsec`, `r/bugbounty`, `r/AskNetsec`, `r/ReverseEngineering`, `r/websecurity`, `r/blueteamsec`
- Read it with: `https://sh.reddit.com/r/{sub}/top/?t=year`, then parse `shreddit-post` attributes.
- Take: field reports, disclosure stories, salary and program discussion, methodology threads with real numbers.
- Watch out: cold requests get a challenge page titled "Welcome to Reddit". Warm the subreddit first. Comment text is mixed into the post element, so trim before quoting.
- Useful attributes: `post-title`, `permalink`, `author`, `created-timestamp`, `score`, `comment-count`.
- Search operators: `site:reddit.com/r/bugbounty "first bounty"`, `site:reddit.com/r/netsec "writeup" <cve>`.

### HackerOne Hacktivity

- URL: `https://hackerone.com/hacktivity`
- Platform: Vue app over a public disclosure grid
- Read it with: `https://hackerone.com/hacktivity/overview?queryString=disclosed%3Atrue&sortField=latest_disclosable_activity_at&sortDirection=DESC&pageIndex=0`
- Take: report titles, program, weakness, severity, bounty, status, researcher handle, short public summary.
- Watch out: report bodies often require a login. The grid metadata is public. Do not describe a report body you did not open.
- Filters live in `queryString`. Examples: `disclosed:true`, `severity:critical`, `program:shopify`.
- This is the best source for current weakness distribution. In the build snapshot of 100 disclosed reports, the top weakness was "Improper Access Control - Generic" (16), followed by "Information Disclosure" (8), "Uncontrolled Resource Consumption" (7), and "SQL Injection" (7).

### Intigriti researchers blog

- URL: `https://www.intigriti.com/researchers/blog`
- Read it with: the listing page, RSS at `/researchers/blog/feed`, category pages such as `/hacking-tools` and `/bug-bytes`.
- Take: tool reviews, hacker spotlight interviews, challenge writeups, bug bytes roundups.
- Watch out: some posts are platform news. The hacker spotlight category is the best source for how individual hunters work.

### PortSwigger Research

- URL: `https://portswigger.net/research`
- Read it with: the listing, author pages such as `/research/james-kettle`, and RSS at `/research/rss`.
- Take: web exploitation primitives, request smuggling, cache poisoning, XSS parser tricks, desync, SAML and OAuth bypasses.
- Watch out: the research index includes topic hubs and author pages. Filter to article URLs before counting.
- Related: Web Security Academy at `https://portswigger.net/web-security` for labs that match the research.

### ProjectDiscovery blog

- URL: `https://projectdiscovery.io/blog`
- Read it with: the listing and author pages at `/blog/authors`.
- Take: recon workflow, nuclei template design, scanner benchmarks, AI pentest results with real CVE walkthroughs.
- Watch out: product announcements outnumber method posts. Filter by category when the listing supports it.

### YesWeHack blog

- URL: `https://www.yeswehack.com/blog`
- Sections: `learn-bug-bounty`, `dojo`, `lab`, `security-best-practices`
- Take: beginner-to-intermediate methodology, Dojo challenge writeups, program lessons.
- Watch out: the old `/learn-bug-bounty` path returns 404. Use `/blog/learn-bug-bounty`.

### GitHub topics and repositories

- URL: `https://github.com/topics/{topic}`
- Useful topics: `bug-bounty`, `recon`, `penetration-testing`, `red-team`, `osint`, `fuzzing`, `rce-exploit`, `exploit`, `payload`, `subdomain`, `web-security`, `reverse-engineering`
- Take: tool names, descriptions, stars, language, install commands, flags.
- Watch out: topic tags are noisy. A popular project tagged `bug-bounty` may be a dashboard or a newsletter. Verify that the repo does offensive work before adding it to a tool chain.
- Stars are a snapshot. Read the last release date before trusting a tool.

### DEF CON media

- URL: `https://media.defcon.org/`
- Read it with: the year directories and the DEF CON RSS feed at `https://defcon.org/defconrss.xml`.
- Take: talk titles, slides, video links by year.
- Watch out: the media server is large. Index the directory, do not download every archive.

## Tier 2: public references

### Hacker News

Harvested snapshot: 95 rows through the Algolia API.

- URL: `https://news.ycombinator.com/`
- API: `https://hn.algolia.com/api/v1/search?query=ssrf&tags=story`
- Take: practitioner discussion, postmortems, tool launches, skeptical comments.
- Watch out: popularity is not accuracy. Follow the linked primary source.

### Lobsters

Harvested snapshot: 25 rows from the security tag.

- URL: `https://lobste.rs/`
- API: `https://lobste.rs/hottest.json`, `https://lobste.rs/t/security.json`
- Take: smaller, more technical discussion than Hacker News.
- Watch out: low volume. Good for depth, poor for breadth.

### Full Disclosure

Harvested snapshot: 16 rows from the current index.

- URL: `https://seclists.org/fulldisclosure/`
- Take: raw advisories, exploit posts, vendor responses.
- Watch out: unmoderated and uneven. Verify the affected version in the vendor advisory.

### Packet Storm

- URL: `https://packetstormsecurity.com/`
- Take: exploits, advisories, tools, papers.
- Watch out: old material is mixed with new. Check the date and the affected version.

### Exploit-DB

- URL: `https://www.exploit-db.com/`
- Take: public exploit code, proof-of-concept commands, affected versions.
- Watch out: many entries are old or unverified. Run them only in a lab.

### Vulners

- URL: `https://vulners.com/`
- Take: aggregated advisories and exploit intelligence.
- Watch out: the free interface limits results. Use it to find the primary advisory, not as the citation.

### NVD and CVE.org

- URLs: `https://nvd.nist.gov/`, `https://www.cve.org/`
- Take: CVE identifiers, CVSS, affected products, references.
- Watch out: NVD enrichment can lag. Check the CVE record and the vendor advisory.

### MITRE ATT&CK, CWE, CAPEC

- URLs: `https://attack.mitre.org/`, `https://cwe.mitre.org/`, `https://capec.mitre.org/`
- Take: technique IDs, weakness taxonomy, attack patterns. Use these IDs in reports and skills.
- Watch out: ATT&CK describes adversary behavior, not a test procedure. Pair it with a method card.

### OWASP

- WSTG: `https://owasp.org/www-project-web-security-testing-guide/latest/`
- MASTG: `https://mas.owasp.org/MASTG/`
- ASVS: `https://owasp.org/www-project-application-security-verification-standard/`
- Cheat Sheets: `https://cheatsheetseries.owasp.org/`
- Take: test cases, verification requirements, defensive guidance.
- Watch out: the guides are broad. Use them as a checklist, not as a payload source.

### HackTricks

- URL: `https://book.hacktricks.wiki/`
- Take: dense command references for Linux, Windows, AD, cloud, web.
- Watch out: commands assume a context. Read the prerequisite section before copying.

### PayloadsAllTheThings

- URL: `https://github.com/swisskyrepo/PayloadsAllTheThings`
- Take: payload lists and bypass tables by vulnerability class.
- Watch out: payload lists age. Confirm the parser and version before blaming the payload.

### Bugcrowd VRT

- URL: `https://bugcrowd.com/vulnerability-rating-taxonomy`
- Take: severity vocabulary and report classification used by many programs.
- Watch out: each program can override the taxonomy. Read the program brief.

### Conference archives

- Black Hat: `https://www.blackhat.com/html/archives.html`
- BSides: `https://bsides.org/`
- OWASP Global AppSec: `https://owasp.org/events/`
- Take: slides and papers with novel primitives.
- Watch out: talk titles overpromise. Find the paper or the video before citing a technique.


### watchTowr Labs

- URL: `https://labs.watchtowr.com/`
- Feed: `https://labs.watchtowr.com/rss/`
- Harvested snapshot: 15 rows.
- Take: pre-auth appliance RCE, patch bypasses, edge device memory bugs, exploit chains.
- Watch out: vendor-heavy and CVE-specific. Check the affected version and the patch.

### Horizon3 Attack Research

- URL: `https://horizon3.ai/attack-research/`
- Feed: `https://horizon3.ai/feed/`
- Harvested snapshot: 10 rows.
- Take: CVE deep dives, exploitability analysis, patch diff walkthroughs.
- Watch out: some posts are product-led. Read the technical section.

### Trail of Bits blog

- URL: `https://blog.trailofbits.com/`
- Feed: `https://blog.trailofbits.com/feed/`
- Harvested snapshot: 20 rows.
- Take: audits, fuzzing, cryptography, supply chain, tooling.
- Watch out: high quality, low volume. Do not skim the method.

### Google Project Zero

- URL: `https://googleprojectzero.blogspot.com/`
- Feed: `https://googleprojectzero.blogspot.com/feeds/posts/default?alt=rss`
- Take: deep vulnerability research and disclosure process.
- Watch out: long posts. Read the abstract and the root cause first.

### NCC Group Research

- URL: `https://research.nccgroup.com/`
- Take: audits, protocol work, cloud and hardware.
- Watch out: the feed path changes. Use the listing.

### Bugcrowd blog

- URL: `https://www.bugcrowd.com/blog/`
- Take: program lessons, VRT updates, researcher stories.
- Watch out: the feed redirects to HTML. Parse the listing.

### Bugtraq and WebAppSec

- URLs: `https://seclists.org/bugtraq/`, `https://seclists.org/webappsec/`
- Take: raw advisories and old disclosure.
- Watch out: both lists are largely dormant. Use them for history, not current CVEs.

### Infosec Mastodon

- URL: `https://infosec.exchange/`
- Take: researcher announcements, quick threads, links to long-form work.
- Watch out: the search API needs a token on some instances. Use the web search or the public timeline.

## Tier 3: login or human in the loop

### HackForums

- URL: `https://hackforums.net/`
- Platform: forum behind Cloudflare
- Read it with: headful Camoufox under Xvfb, a persistent profile, and a wait loop. A cold headless request returns 403 and the title "Just a moment...". A working command:

  ```bash
  xvfb-run -a ./beril harvest forums --sources hackforums --headful \
    --profile profile/hackforums --limit 30
  ```

- The challenge clears in about 15 to 40 seconds when the IP is not already flagged. If it stays on "Just a moment", delete the profile, use a new profile directory, and try a residential or mobile connection. Repeated attempts from the same address make the block longer.
- Take: beginner questions, marketplace chatter, old exploit threads, forum sections such as Website Hacking (fid 43), Beginner Hacking (fid 4), Hacking Tools (fid 10), and Blackhat / Red Team (fid 447).
- Watch out: quality varies and the marketplace has scams. The homepage feed is mostly advertisements. Parse a section page and filter ad titles before you build a reading list.

### Discord communities

- ProjectDiscovery, Bugcrowd, Intigriti, Hack The Box, and many tool projects run Discord servers.
- Take: release notes, quick answers from maintainers, early bug reports.
- Watch out: not indexable without auth. Do not scrape member lists. Search the public answer channels manually and cite the tool docs instead.

### X and LinkedIn

- Take: announcements, threads, hiring and conference signals.
- Watch out: login walls and unstable HTML. Use a public profile URL as the citation, not a scraped timeline.

### Private program pages

- HackerOne, Bugcrowd, Intigriti, YesWeHack, Synack.
- Take: scope, rewards, report requirements.
- Watch out: program data can be private. Do not publish scope details that the program has not made public.

## Query patterns that work

Search engines still beat most site search for cross-source work.

```text
site:0x00sec.org "<technique>" writeup
site:reddit.com/r/netsec "<cve>" analysis
site:github.com "nuclei-templates" "<product>"
site:portswigger.net/research "<parser>" bypass
"HackerOne" "<vulnerability class>" "bounty" 2025
```

For GitHub code and secrets:

```text
org:<company> "BEGIN RSA PRIVATE KEY"
"<company>.com" "api_key" extension:env
path:.github/workflows "pull_request_target"
```

For disclosed report discovery:

```text
site:hackerone.com/reports "<product>" "resolved"
site:hackerone.com/reports "SSRF" "internal"
```

## Etiquette

1. Read the pinned rules before posting or scraping.
2. Do not republish a paywalled or private report.
3. Credit the researcher and link the original.
4. Do not mass-message authors for a summary. Read the article.
5. Do not scrape member profiles or email addresses.
6. Keep one request every second or two per host.
7. If a site offers RSS or JSON, use it before parsing HTML.
8. Record the date. A forum answer from 2019 may describe a patched behavior.

## Source quality

| Grade | Meaning | Example |
|---|---|---|
| A | Primary evidence, reproducible | Vendor advisory, researcher post with code and a repro |
| B | Credible secondary account | Conference talk, detailed writeup, disclosed report |
| C | Lead or opinion | Forum question, social post, newsletter summary |

A method card should rest on at least one A or two independent B sources. A C source can start a search but cannot close it.
