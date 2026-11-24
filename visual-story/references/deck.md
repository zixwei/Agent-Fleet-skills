# Pitch deck with micro-animations, transitions and a PDF

A deck shown from the browser: every slide builds itself with an animation over 2–4 s, slides change with a branded transition, and a static PDF of the same deck sits next to it. For a plain conference talk without scenes and transitions, use a regular slide deck workflow; the readability and layout checks below apply there too.

## 1. Input: questions for the user

Ask in one batch, recommended option first:

| Question | Why ask |
|---|---|
| Who sees it and where: jury, investor, a hall, a link | text density and size; it must read from the back row of a big hall |
| Slide language | headlines are fitted to the line length of that language |
| Where the numbers come from | metrics only from the user's source, never invented |
| Brand style: colors, fonts, a reference page | the deck follows the brand, not a neutral template |
| Is a PDF needed | the PDF is raster: links in it are not clickable, addresses become QR codes |
| Images: generate? budget? source | generation costs money; the user's photos are never sent to outside services |
| Cover and finale: who is on the cover, what goes last | for a pitch: the founder on the cover, the numbers and the QR on the last slide |

The script is a table of slides: number, headline without a period, one idea, one image. Every slide must carry value for the person it is shown to; drop the extra ones. Show the table, wait for "yes", then build 1–2 trial slides.

## 2. Skeleton

```
deck/
├── index.html     # <section class="slide s-<name>" data-theme data-label data-tr="wipe|drop|iris">
├── deck.css       # 1920×1080 stage, slide themes, loops only on .is-on, .print mode
├── deck.js        # fitting, navigation, transitions, scene timelines, print mode
├── vendor/        # gsap.min.js, QR generator — local, no CDN
├── fonts/         # woff2, scripts split by unicode-range
├── img/           # final .webp; generation sources in img/raw/, never deployed
└── tools/         # image generation script and prompt specs
```

- **A 1920×1080 stage** is fitted to the window with `scale(min(W/1920, H/1080))`. All sizes are in stage pixels.
- GSAP and fonts are local: the showing may happen without internet.

## 3. A slide is a timeline

```js
scenes['s-name'] = root => {
  const tl = gsap.timeline({ paused: true });
  tl.from(root.querySelectorAll('.ln > *'), { yPercent: 112, duration: .7, ease: 'power4.out', stagger: .14 });
  // ...
  return tl;
};
```

- When a slide is shown, its timeline runs `restart()`; all others run `pause(0)`. Keep counter resets in `tl.data`.
- Helpers from the reference deck (→ `examples.md`, `deck.js`):
  - `head` — headline lines slide up out of a `.ln{overflow:hidden}` mask;
  - `rise` — `y:150`, `back.out(1.7)`;
  - `slap` — a sticker, `scale 3.2→1` with a shake;
  - `counter` — rolling digits.
- Permanent loops (`bob`, `sway`, `squash`) are CSS keyframes only on `.is-on`: they do not spin on hidden slides.
- Packets along scheme wires use SVG `animateMotion`. Build procedural graphics with scatter from a fixed seed: the deck must look the same at every showing.

## 4. Transitions (`data-tr`)

| Transition | How | Timing |
|---|---|---|
| `wipe` | three stripes in brand colors, `scaleX`; the slide changes under the closed stripes, the stripes leave in reverse order | 0.3 s, `power3.inOut`, 0.055 s stagger between stripes |
| `drop` | the same, vertical, `scaleY` | the same |
| `iris` | a 2300 px circle in the next slide's color, `scale 0→1`, swap, fade out | 0.5 s + 0.22 s |

Do not lose key presses during a transition: queue them. Honor `prefers-reduced-motion` in both CSS and GSAP (`gsap.globalTimeline.timeScale` or an instant `progress(1)`).

## 5. Text and sizes

- Large and bold: headlines 132–310 px on a 1920 stage, captions no smaller than 29 px. Do not make things small.
- No period at the end of a headline.
- `data-fit="1808"`: after `document.fonts.ready`, shrink the font size in 2 px steps until the longest line fits, but not below 60 px. Wrapping is forbidden (`white-space: nowrap`).
- Fonts: `preload` and `font-display: block`, otherwise fitting measures the fallback font.

## 6. Images

- Source: your image generation API. Keep the key in an environment variable, never in the deck folder.
- Prompt structure: Scene / Subject / Important Details / Use Case / Constraints, the brand style and hex colors, a transparent background (`background: "transparent"`), 2k.
- Two variants per image; choose by eye on the slide. Rejected variants are not deployed.
- A person on the cover is a stylized figure built from a verbal description; never send their photo to outside services.
- Generation costs money: agree on the budget and get a "yes" before the first image.

## 7. Buttons, QR, keys

- QR: either a generator in `vendor/` builds it at runtime (an SVG path, `shape-rendering: crispEdges`) or a prebuilt SVG from `segno` (→ `publish.md`). Check with `zbarimg`.
- A small button at the top right opens the related site (the map, the product) in a new tab. A fullscreen button on the title slide, hidden under `:fullscreen` and in print.
- A click on a link or button does not flip the slide.
- Keys:
  - → and Space — forward, ← — back;
  - F — fullscreen, R — replay the slide animation; duplicate both for non-Latin layouts;
  - a number + Enter — jump to that slide;
  - a click on the left quarter of the screen — back.
- Browsers allow fullscreen only from a real click: it cannot be checked automatically, so tell the user that.

## 8. PDF (raster)

1. `?print=1` mode: slides in a column without `scale()` (each exactly 1920×1080), every scene at its final frame (`tl.progress(1)`), SMIL frozen (`svg.pauseAnimations(); svg.setCurrentTime(<end>)`), CSS `animation: none !important`; the deck's shared top bar (logo, slide number) repeated on every slide.
2. Full-page capture:
   ```bash
   agent-browser --session $S set viewport 1920 1080
   agent-browser --session $S open "http://127.0.0.1:<port>/index.html?print=1&t=$(date +%s)"
   agent-browser --session $S wait 2500
   agent-browser --session $S screenshot --full all.png
   ```
3. `magick all.png -crop 1920x1080 +repage s%02d.png`
4. `magick s??.png -units PixelsPerInch -density 144 -compress jpeg -quality 92 deck.pdf`

`agent-browser pdf` produces Letter pages and ignores `@page`; do not use it. Review the PNG pages, not the PDF: `magick` cannot open a PDF without Ghostscript. 15 slides at `-quality 92` weigh ~3.7 MB; keep it ≤ 10 MB for email, and drop `-quality` to 85 if it is heavier.

## 9. Checking

- Static: a frame of every slide in `?print=1` (→ `qa.md`).
- Live animation: flip through the deck with the arrow keys and capture the middle (after 1.5 s) and the end (another 3.6 s later) of every slide, montage `3x2`.
- `agent-browser errors` shows console errors.
- Layout checklist: nothing overlaps, no word is broken across lines, nothing is clipped, every caption is at least 29 px on the stage.

## 10. Publishing

- Cloudflare Pages (→ `publish.md`): `rsync` into `dist` without `tools/`, `img/raw/` and rejected variants; `_headers` with `X-Robots-Tag: noindex`.
- A subfolder on another site (Vercel, like `/pitch/`): put a `location.replace(path + "/")` script in `<head>` for the address without a trailing slash, otherwise relative paths break. Before `vercel deploy --prod`, compare sha256 of the live and local files: a deployment is a full snapshot of the folder, and a subfolder someone else did not commit will disappear. Roll back with `vercel rollback`.
- Check every new file with `curl -sI` on that specific deployment's address: on the main address a missing file comes back as `200 text/html` (`index.html` is served instead).
- One deck lives in one place. Two copies at different addresses drift apart.

## Pitfalls

| Symptom | Cause | Right step |
|---|---|---|
| Empty spaces in the PDF where animated blocks should be | print mode did not run the scenes to their end | `progress(1)`, `setCurrentTime`, force "live" elements on |
| The PDF has the wrong page size | `agent-browser pdf` and `@page` | raster, by the recipe in section 8 |
| Links in the PDF do not click | the PDF is raster | warn the user; addresses become QR codes |
| A headline runs off the edge | fitting measured the fallback font | `data-fit` after `fonts.ready`, `font-display: block` |
| An old image after replacing it | cache | `?v=N` on every asset, bumped on every deploy |
| `/pitch` without a slash loads without styles | relative paths | redirect to `/pitch/` in `<head>` |
| Slides with numbers "for volume" | numbers with no value for the viewer | every number is valuable to the person it is shown to, or it is gone |
| Periods in headlines | a prose habit | no periods in headlines anywhere |
