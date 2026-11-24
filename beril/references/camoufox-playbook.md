# Camoufox playbook

Camoufox is a Firefox build with patches for fingerprint consistency, plus Playwright's API. It is not an invisibility cloak. It works because the browser looks ordinary: a real Firefox engine, a coherent locale and timezone, a real rendering path, and human-like input timing.

This playbook covers the settings that matter for security research, the order to try when a site pushes back, and recipes for the site types Beril targets.

## Install and health check

```bash
python -m venv .venv
source .venv/bin/activate
pip install -U "camoufox[geoip]" beautifulsoup4 lxml
camoufox fetch
python -c "from camoufox.sync_api import Camoufox; print('ok')"
```

Linux libraries for a headful run:

```bash
sudo apt install -y libgtk-3-0 libx11-xcb1 libasound2t64 xvfb
```

If `camoufox fetch` fails behind a proxy, set `HTTPS_PROXY` and run it again. The browser lives in `~/.cache/camoufox` by default.

## Settings that matter

```python
from camoufox.sync_api import Camoufox

with Camoufox(
    headless=True,
    humanize=True,          # human-like cursor and typing timing
    locale="en-US",
    geoip=True,             # timezone and geolocation from the exit IP
    os="linux",             # keep this consistent with the host
    block_images=True,      # faster pages, fewer fingerprint leaks from image loading
    i_know_what_im_doing=True,  # silence the canvas-noise warning
) as browser:
    page = browser.new_page()
    page.goto("https://example.com", wait_until="domcontentloaded")
```

### Fingerprint consistency

A mismatch is worse than a common fingerprint. If the user agent says Windows, the fonts and GPU should not say Linux. If the locale is `en-US`, the timezone should be a US timezone unless GeoIP says otherwise.

Rules:

1. Pick one OS per session and keep it.
2. Let GeoIP set the timezone from the proxy. Do not hardcode `Europe/Berlin` while exiting through Virginia.
3. Do not rotate user agents between requests in one session. Camoufox derives a coherent identity already.
4. Use one proxy per identity. A residential proxy that changes country mid-session breaks the locale story.
5. Avoid `canvas_noise` unless you need it. The default is fine and the warning is expected.

### Headless or headful

Start headless. Move to headful when a challenge survives three headless attempts.

```bash
xvfb-run -a python scripts/harvest.py forums --sources hackforums --headful
```

`xvfb-run` gives Firefox a display without a desktop. Some Cloudflare and DataDome challenges behave differently when a real window exists, even under Xvfb.

### Persistent profile

Cookies and a prior challenge clearance can be reused:

```python
with Camoufox(
    headless=True,
    humanize=True,
    persistent_context=True,
    user_data_dir="profile/hackerone",
) as browser:
    page = browser.new_page()
```

Use one profile directory per site. A shared profile across unrelated sites creates a browsing history that does not match a new visitor.

## The challenge ladder

Work down the list. Stop as soon as the page returns real content.

| Step | Action | Cost |
|---|---|---|
| 1 | Request the JSON or RSS endpoint directly | Lowest |
| 2 | Headless Camoufox, wait for a content selector | Low |
| 3 | Warm the origin, then navigate to the deep page | Low |
| 4 | Wait 8 to 15 seconds after `domcontentloaded` | Low |
| 5 | Headful under Xvfb | Medium |
| 6 | Persistent profile with prior cookies | Medium |
| 7 | Click the challenge checkbox in its iframe | Medium |
| 8 | Pause for a human to clear it, then save the profile | High |
| 9 | Mark the source unreachable in the dossier | Last resort |

Do not reload a challenge page every two seconds. Each reload looks like a bot and can extend the block.

### Reading a Cloudflare page

A 403 with the title "Just a moment..." is a challenge, not a missing page. Check for these markers:

```python
html = session.get(url)
if html and ("Just a moment" in html or "cf-chl" in html or "challenge-platform" in html):
    print("challenge")
```

Wait longer before declaring failure:

```python
page.goto(url, wait_until="domcontentloaded")
page.wait_for_timeout(12_000)
if page.query_selector("iframe[src*='challenges.cloudflare.com']"):
    frame = page.frame_locator("iframe[src*='challenges.cloudflare.com']")
    box = frame.locator("input[type=checkbox]")
    if box.count():
        box.first.click()
```

Only click when the checkbox exists. Some challenges are invisible and clear on their own.

### When a site returns a download

RSS endpoints sometimes arrive as a download instead of a page. Playwright raises `Download is starting`. Fetch from page context instead:

```python
session.page.goto("https://0x00sec.org/")
xml = session.page.evaluate("u => fetch(u).then(r => r.text())", "https://0x00sec.org/rss/")
```

The origin must match for CORS. Warm the site root first.

## Site recipes

### Discourse forums

Discourse exposes JSON for almost everything. Use it.

```text
https://forum.example.com/latest.json
https://forum.example.com/top.json?t=year
https://forum.example.com/t/{slug}/{id}.json
https://forum.example.com/search.json?q=ssrf
```

The listing gives `title`, `slug`, `id`, `posts_count`, `views`, `created_at`, and `excerpt`. It is faster and cleaner than parsing the HTML.

Pagination: `?page=1`, `?page=2`. Stop when a page repeats topic IDs.

### Ghost blogs and RSS

Ghost sites such as 0x00sec publish feeds at `/rss/` and per-tag feeds at `/tag/{tag}/rss/`.

```python
items = session.rss("https://0x00sec.org/tag/redteam/rss/")
for item in items:
    print(item["title"], item["url"], item["author"])
```

Ghost's `dc:creator` is often the site editor, not the author. Read the first paragraph for a byline. Store the editor as `author` only if you cannot find a person.

### Reddit

The old and new interfaces both challenge cold requests. Warm the subreddit, then use the shadow DOM host element.

```python
session.get("https://sh.reddit.com/r/bugbounty/", "text")
html = session.get("https://sh.reddit.com/r/bugbounty/top/?t=year", "html", wait_ms=3500)
soup = BeautifulSoup(html, "html.parser")
for post in soup.select("shreddit-post"):
    print(post.get("post-title"), post.get("permalink"), post.get("score"))
```

Useful attributes: `post-title`, `permalink`, `author`, `created-timestamp`, `score`, `comment-count`.

The comment text is inside the same host element. When extracting a post body, cut at the first comment marker or use the post's own `div[slot="text-body"]`.

### HackerOne Hacktivity

The public grid is a Vue app. Each card carries `data-testid="hacktivity-item"`.

```python
for item in soup.select('[data-testid="hacktivity-item"]'):
    report = item.select_one('a[href*="/reports/"]')
    user = item.select_one('a[href*="type=user"]')
    lines = [clean(x) for x in item.get_text("\n", strip=True).splitlines() if clean(x)]
    # lines: program, weakness, severity, bounty, status, title, "Bug reported by", handle, age, summary
```

The report body is often behind a login. The grid metadata is public and enough for trend work. Do not claim you read a report when you only read its summary.

Pagination uses `pageIndex=0`, `pageIndex=1`, and so on. Filters live in `queryString`, for example `disclosed:true` or `severity:critical`.

### GitHub topics and repositories

Topic pages render repository cards as `article` elements. Star counts appear in the card text as `Star 12.3k`.

```python
stars = re.search(r"Star\s+([\d.,]+k?)", art.get_text(" ", strip=True))
```

Repo pages embed `"stargazerCount":12345` in the HTML. That is more precise than the card. Read the raw README from `https://raw.githubusercontent.com/{owner}/{repo}/HEAD/README.md` and pull install lines from it.

Rate limits: unauthenticated REST API allows 60 requests per hour. Scraping the HTML page has no such limit, but be polite.

### Hacker News and Lobsters

Both have stable public APIs and no challenge:

```text
https://hn.algolia.com/api/v1/search?query=bug+bounty&tags=story
https://lobste.rs/hottest.json
```

Use them as lead generators, then follow the linked article.

## Parsing through the live page

Static HTML misses shadow DOM and client-rendered content. When a selector exists in DevTools but not in `page.content()`, read it through Playwright.

```python
body = session.text_of("shreddit-post")
heads = session.headings("shreddit-post h2, shreddit-post h3")
```

For content inside an iframe:

```python
frame = page.frame_locator("iframe#content")
text = frame.locator("body").inner_text()
```

For infinite scroll:

```python
for _ in range(5):
    page.mouse.wheel(0, 4000)
    page.wait_for_timeout(1200)
```

Stop when the scroll height stops changing.

## Caching and evidence

Every fetch in `camo.py` writes a cache file keyed by the URL hash. A second parse costs nothing.

For claims that may change, use `snapshot()`:

```python
info = session.snapshot(url, folder="evidence", label="h1-3923212")
# saves .html, .txt and .png
```

Record the URL, the retrieval time, and the HTTP status. If the page changes later, the snapshot is your proof of what you saw.

## Politeness and rate limits

- One request every 1 to 2 seconds per host.
- Randomize within that window.
- Back off 5, 10, 20 seconds on 429 or 503.
- Stop after three failed attempts and record the failure.
- Do not run more than two browser sessions against the same host.
- Respect `Retry-After` when present.
- Check robots.txt for paths you plan to crawl at scale.

```python
import urllib.robotparser
rp = urllib.robotparser.RobotFileParser()
rp.set_url("https://example.com/robots.txt")
rp.read()
print(rp.can_fetch("*", "https://example.com/research/"))
```

## Failure modes

| Symptom | Likely cause | Fix |
|---|---|---|
| Blank body, 200 status | JavaScript app not finished | Wait for a content selector, not `networkidle` |
| 403 with "Just a moment" | Cloudflare challenge | Headful, longer wait, click checkbox, persistent profile |
| `Download is starting` | RSS or file response | Fetch from page context or use a direct HTTP client |
| Empty list from BeautifulSoup | Shadow DOM | `inner_text` on the host element |
| Redirect to another locale | GeoIP and locale disagree | Set locale to match the exit country |
| Same page on every page index | Filter ignored | Check the query string against the site's own UI |
| Content appears only after scroll | Lazy loading | Scroll and wait, or use the site's JSON |
| 429 after a few requests | Too fast | Back off and reduce concurrency |
| Login wall | Private data | Stop. Do not bypass. Record the limit |

## Minimal client

When the harvesters are too much, this is enough:

```python
from camoufox.sync_api import Camoufox
from bs4 import BeautifulSoup

with Camoufox(headless=True, humanize=True, geoip=True, i_know_what_im_doing=True) as browser:
    page = browser.new_page()
    page.goto("https://example.com/research", wait_until="domcontentloaded")
    page.wait_for_selector("article", timeout=20000)
    html = page.content()
    soup = BeautifulSoup(html, "html.parser")
    for h in soup.select("article h2"):
        print(h.get_text(" ", strip=True))
```

Keep the page open while you extract. Closing the browser before reading the DOM is the most common mistake in a first Camoufox script.
