# Explainer film: scenes, camera, captions

An interactive film in the browser: a system or a process is laid out as scenes on one huge canvas, the camera flies between them, and a caption at the bottom says what is happening right now. The presenter talks and clicks through scenes live; a viewer can drag the canvas and open the "full scheme" map. The same film exports to MP4 (→ `video-export.md`).

## 1. Input: questions for the user

Ask in one batch, recommended option first:

| Question | Why ask |
|---|---|
| How is it shown: presenter clicks scenes / autoplay / video only | decides the control bar and caption length |
| Theme: light like the product's website, or dark | the palette comes from the product's site, not from your head |
| Data: fictional example or real | a public film uses only fictional people, sums and dates |
| Code: "image large, code small" or the other way round | code in frame reads only as a small line under the image |
| Pilot scene | one scene finished before all the others |
| Deadline and where it is shown | broadcast, big screen, phone; decides how much of `responsive.md` you need |

The script is a table of scenes: number, title (3–5 words), "what this part is about" in one line, 5–10 captions. Show the table and wait for "yes".

## 2. File skeleton

```
film/
├── index.html      # top bar, <svg> canvas, scene card, caption, bottom: scene list and scrubber
├── film.css        # site palette and fonts, overlays, phone layouts
├── kit.js          # primitives: el/txt, easing curves, K(), appear, draw, spring, flight, pill, doc
├── engine.js       # scene assembly, film and viewer cameras, captions, full-scheme map, keys
├── s-<group>.js    # 2–3 scenes per file; each pushes itself into window.FILM_SCENES
├── fonts/          # local woff2
└── deploy.sh       # build .dist and publish (→ publish.md)
```

Plain SVG and JS, no bundler: the file opens from disk and a change shows after reload.

## 3. A scene is a pure function of time

```js
FILM_SCENES.push({
  n: 4, title: 'Scene title', dur: 38,
  origin: [10800, 0],                 // where the scene sits on the shared canvas
  bounds: [0, 0, 2800, 1600],         // its extent for the full-scheme map
  cam: [[0, 1400, 800, 1500], [12, 900, 700, 900]],   // [time, cx, cy, visible world height]
  caps: [[0.6, 'One-line caption'], [5.4, 'Step 2: …', 'CONST_IN_CODE = 8']],
  shakes: [[15.7, .3]],              // an "impact": time, strength
  build(g, S) { /* once: create elements, keep them in S */ },
  render(lt) { /* every frame: everything from lt, accumulate nothing */ },
});
```

- **Step:** in `render(lt)` compute every property from the local time `lt`: `K(lt, a, b, E.out)`, `appear(lt, a, b)`, `draw(path, k)`.
  **Key point:** no "already shown" flags, timers or CSS animations inside a scene.
  **Why:** then scrubbing, pause, 0.25× speed, a URL like `?s=6&t=40` and frame-by-frame video export all work for free. One stateful scene breaks scrubbing backwards.
- **Step:** the engine calls `render` only for scenes whose `lt` changed; scenes out of frame are dimmed to 8% opacity.
- **Step:** the full-scheme map draws every scene in its final state (`render(dur)`).

## 4. Camera

- Keys are `[t, cx, cy, h]`; `h` is how much world height is visible. Scale: `s = min(AH / h, SW / (h · 16/9))`, where `AH` is the canvas height minus the margins for the scene card on top and the caption at the bottom.
- Between keys use the soft curve `(1 − cos πx) / 2`, and interpolate scale in log space — otherwise the zoom-in jerks at the end.
- The engine builds the flight between scenes: a middle key pulled back to `h · 1.7 + distance · 0.25`, and the scene's first key moved to ≥ 1.6 s. The viewer sees where we fly from and to.
- Short segments (< 3 s) use a quartic `inOut`, long ones a sine `inOut`; a linear segment for a fly-by without braking.
- UI margins in the frame calculation are mandatory: the camera knows where the caption is and frames the shot above it. Toggling captions recomputes the margins.
- Shake from an "impact" is a decaying sum of sines over 0.6 s, with no random numbers — otherwise video frames will not match. A "handheld" camera is the same idea: `dx = 7·sin(.37t) + 3·sin(.91t + 2)`.
- Viewer camera on top of the film: drag to pan, wheel or two fingers to zoom; the "100%" button and double click return to the film camera. Grabbing starts exactly from the frame on screen (the current blended camera), otherwise the frame jumps; smoothing `1 − exp(−dt·12)`. Play or a jump to a scene eases back to the film camera.

## 5. Motion

| What | How | Why |
|---|---|---|
| Object enters | from 0.9 scale and transparent, slight overshoot `back(1.7)`; enter 0.55 s, exit 0.3 s | nothing appears from zero; entering is slower than leaving |
| Movement | `inOut = bezier(.77,0,.175,1)`, flight along an arc with anticipation and a squash on landing | Disney-style motion, not a ruler |
| Response, highlight | `out = bezier(.23,1,.32,1)` | fast start, soft finish |
| Line and arrow | `pathLength=1`, `stroke-dashoffset` from 1 to 0; the arrowhead turns on at k ≥ 0.985 | otherwise the arrowhead hangs there first and the line draws in later |
| Accent | ring flash 0.7 s, square particles 1.1 s with gravity | juice without blinking |
| Caption change | blur 4 px + 6 px shift, 220 ms | two captions never overlap |
| Particles along a lit line | 55 units/s, one per 90 units, ends fade over 24 units | the direction of flow is visible |
| Glow | one `<use>` with blur 5 on top of the highlight group | a filter on every line kills the frame rate |
| Scheme reveal | `appearAt = base + x / width · 3.2` | the scheme builds as a wave left to right, in reading order |
| Strict intro | `scaleX` line → title wipe with `clip-path` and a caret → mono kicker typed in | letters flying in with blur and a heavy display font read as a cheap effect |

Curves and primitives live in the reference film's `kit.js` (→ `examples.md`); do not invent new curves, take them from this table.

## 6. Captions

- One line, up to ~70 characters. Step number "2/7" in a green chip on the left; code (a constant name, path, command) in small monospace under the text.
- The caption says **what is happening now** and **when** it happens. Every number has a noun: not "8 cards" but "up to 8 cards with new facts per model call".
- The caption lives in the lower third; the canvas reserves a `BOT` margin under it so it never covers the scene.
- Remove annotations and caveats from the canvas: all explanation goes into the caption, the canvas carries only the image. No caveats about the future ("still growing", "built after December"), no control hints, no calls to action ("Scan with your phone"): something not yet built is shown with a dashed outline and a pale tone, not with words.

## 7. Full-scheme map

- Grid: rows of 4 scenes, **every row left to right** in number order. Cells and plates are all the same size.
- Plate: number in green monospace, title semibold (700), "what this part is about" regular (400). No numbers in circles: the digits do not fit.
- Compute the threshold for switching into map mode from this screen's frame scale, not as an absolute number: on a phone the frame is smaller, and an absolute threshold switches the plates on over the scene.
- Clicking a plate flies to that scene. To the right of the grid, a column with a QR code to the film page (→ `publish.md`, QR section).
- When fitting the map, hide the scene card and the caption and leave ~60 px at the bottom for the fade.

## 7a. Film of a real process

When the film shows a real process (lanes of actors, gateways, loops), scene functions are replaced by event tables per layer, all in absolute seconds:

- chapters `[t0, captions]`; node activity `[id, a, b, end: done|fail|repair]`;
- gateway decision with a color; a pass along an arrow `[wire, t0, t1, color, token label]`;
- repainting of the travelled segment; run status.

One function `stateOf(events, t)` computes a node's state at time `t`. Color means state (running, revision, repair, approved), with the legend in frame. Lanes by actor type (human, service, script, AI, knowledge, external), phases as labelled dividers "01 ENTRY". Draw the process from its code, with every block and loop.

## 8. Controls and keys

Space — play/pause · ← → — ±1 s (Shift ±5) · PageUp/PageDown, N/P — scenes · 1–9 — scene · M — full scheme · 0 — film camera · F — fullscreen · H — hide panels · C — captions · ? — help.
The film always plays on its own and loops: no stop at scene end and no "Stop" button. A presenter only needs pause: Space or the play button. Speeds 0.25× / 0.5× / 1× / 1.5×; for autoplay the default is 0.5×.

Store viewer settings (theme, language, frame format, "cinema"/"overview" camera, captions, hidden panels) in `localStorage` under a versioned key: when defaults change, bump the key version, otherwise a returning viewer keeps the old ones. Duplicate the keys for non-Latin layouts. At the end the camera flies back to scene 1 (the same `jump` the scene buttons use) and the film plays from zero; if the final frame matches a frame inside the film, loop from there. With `prefers-reduced-motion`, open the film paused on the final frame.

## 9. Fonts and cache

- Fonts are local, `preload`, `font-display: block`. Before the first layout: `await document.fonts.ready`, then warm up — measure text in every text class and every script you use (`getComputedTextLength`) and wait ~160 ms. Without warm-up, pill widths are measured with the fallback font and text overflows.
- Every included file carries `?v=NN`; bump the number on every change you show.

## 10. Order of work

1. Pilot scene, complete: image, motion, captions. Frames (→ `qa.md`), show, wait for "yes".
2. The other scenes in batches of 2–3; show each batch and wait.
3. Full-scheme map, controls, phones (→ `responsive.md`).
4. Publishing (→ `publish.md`), QR, video if needed (→ `video-export.md`).

## Pitfalls

| Symptom | Cause | Right step |
|---|---|---|
| The arrowhead appears first, then the line draws in | `marker-end` is visible from the first frame | hide the marker until k ≥ 0.985 |
| A two-headed arrow where the flow goes one way | an extra `marker-start` | one arrowhead in the direction of flow |
| Vertical colored stripes on the left of cards | a template accent | banned outright; set a card apart with shape, background tone or a topic icon |
| Rows of the full-scheme map zigzag | scenes laid out along the camera path | every row left to right |
| Map plates are bold and digits do not fit in circles | heavy weights and numbers in circles | an even grid, 700/400, the number as text |
| A label everything is clear without ("Scan with your phone" under a QR, "the night does not touch this" on every section, "still growing") | the habit of explaining and hedging | delete it; keep the image, the caption at the bottom says what matters |
| The viewer does not understand a number in frame | a number without a noun and a moment | "up to 8 cards with new facts per call" |
| The caption wraps in the middle of the screen | `left:50%` without a width | `width: max-content; max-width: calc(100% − 48px)` |
| The caption looks washed out | the bottom fade of the canvas sits on top | caption above the fade (`z-index`) |
| The top of the scene card fades | the top gradient sits over the card | scene card above the gradient |
| On a phone, map plates show through the scene | an absolute map threshold | threshold from the frame scale (→ section 7) |
| Two identical `const` names in one scene | copying blocks inside `build` | unique names, run in the browser after every scene |
| Too fast and too little detail | one speed, no pull-back | a speed control and a camera pull-back that shows the whole path |
| The caption covers the scheme | the camera does not know about the caption | a caption margin in the frame calculation |
| The progress bar jumps while the chapter number types in | the line width changes | keep the untyped tail in the line, hidden |
| In a narrow frame format captions overlap the legend | only 16:9 was checked | a contact sheet for every format (→ `qa.md`) |
| Sound is silent at 0.5× | sound durations ignore speed | divide durations by speed |
