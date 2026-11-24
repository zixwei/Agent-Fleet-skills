# 🖼️ Free Stock Photos (Unsplash, Pexels, Openverse)

Use this when building a page (landing page, business site, portfolio…) and the user has **not supplied real photos**. Script: `scripts/stock_images.py`.

## 1. Image priority
1. **The user's real photos** (premises, products, team) — always best. Ask before using stock if the project is real.
2. **Free stock photos** through this script.
3. **CSS/SVG illustration** when there is no network, no key and Openverse also fails. The script exits `2` and prints this advice.

Never paste image URLs "found" on the web without the script: they break, show the wrong thing, or are not licensed for use.

## 2. Setup (once)
| Provider | Environment variable | Where to get a key | Notes |
|---|---|---|---|
| Unsplash | `UNSPLASH_ACCESS_KEY` | unsplash.com/developers → New Application → *Access Key* | High quality, lots of beauty/spa imagery. Must be **hotlinked** (never re-hosted) |
| Pexels | `PEXELS_API_KEY` | pexels.com/api → *Your API Key* | Downloading and self-hosting allowed |
| Openverse | none | — | Openly licensed images (CC0, Public Domain, CC BY, CC BY-SA), filtered to commercial use. Uneven quality: Flickr images are often only ~1024 px wide and some carry a signature/watermark — preview with `search` and refine the query |

`auto` tries Unsplash → Pexels → Openverse depending on which keys exist. With no keys it uses Openverse only.

## 3. Workflow while building a page
1. **List the image slots** from the layout: `hero`, `about`, `service-<name>`, `gallery-1..n`, `team`… 4–10 images usually cover a landing page.
2. **Write specific English keywords** (providers search English best): "nail salon manicure pastel", "hot stone massage spa", "facial skincare treatment", "asian woman hair wash spa". Add `--orientation landscape|portrait|squarish` to match the frame.
3. **Preview:**
   ```bash
   python3 "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/stock_images.py" search "nail salon manicure pastel" --count 6
   ```
4. **Fetch one image per slot** (the above-the-fold image gets `--eager`, the rest are lazy):
   ```bash
   python3 "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/stock_images.py" fetch "spa interior calm" \
     --name hero --out assets/img --orientation landscape --alt "Calm treatment room" --eager
   ```
   The script prints a complete `<img>` tag (`src`, `srcset`, `sizes`, `width`, `height`, `alt`, `loading`/`fetchpriority`) — paste it into the slot.
5. **Credits:** put the contents of `assets/img/CREDITS.html` in the footer (or a "Credits" page). Required for Unsplash, Pexels and CC BY / CC BY-SA images.
6. **Label them as illustrative** when the page presents a real business: stock photos are not its premises, products or customers.

## 4. Provider rules (built into the script)
- **Unsplash:** use only the `images.unsplash.com` URLs the API returns, with size parameters (`w`, `q`, `auto=format`); the script triggers `download_location` when a photo is used; credit "Photo by *name* on Unsplash" with `utm_source=…&utm_medium=referral` links. Never download and self-host.
- **Pexels:** downloads allowed; credit "Photo by *name* on Pexels" with a link to Pexels.
- **Openverse:** only licenses that allow commercial use; credit the title, creator, license and license link (optional for CC0/Public Domain, still recommended).
- On a brand's page, reject photos showing **another brand's** logo, cups, packaging or storefront; if nothing fits, keep an SVG illustration for that slot.
- Never imply that people in stock photos are real staff or customers; never present stock images as real results (medical, before/after treatments).

## 5. Performance and display
- Downloads are converted to **WebP** at 480 / 768 / 1024 / 1600 px (quality 75; the `--eager` hero uses 62 so the mobile LCP image stays around 60 KB) when Pillow is installed (`pip install pillow`); otherwise the original file is kept.
- Always ship `width` and `height` (or `aspect-ratio`) so **CLS = 0**; only the hero uses `fetchpriority="high"`, everything else `loading="lazy"`.
- Text over a photo needs a dark overlay/gradient so it reaches ≥ 4.5:1 contrast; re-check with the checklist in `ui-ux/pro-rules.md`.
- Use `object-cover` with fixed-ratio frames in galleries so mixed photo sizes still line up.

## 6. When it fails
| Message | What to do |
|---|---|
| `UNSPLASH_ACCESS_KEY is not set` / `PEXELS_API_KEY is not set` | Set the key, or let `auto` fall back to Openverse |
| `HTTP 401` | Wrong or revoked key |
| `HTTP 429` | Quota exhausted; wait or switch `--provider` |
| `cannot reach …` | No network or a proxy blocks it; keep the CSS/SVG illustration and tell the user |
