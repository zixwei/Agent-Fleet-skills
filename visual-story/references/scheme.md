# Interactive map of a process or system

One big, detailed map drawn like a whiteboard: from afar you see the whole picture, closer in the details appear, a click on a node opens a window with the details. Sections (1–9) lead through the map like a guided tour. Use it on its own or inside a talk.

## 1. Input

- Draw the map from the **real** process: from code, configs and logs, with every block and loop. From a reference, take only the motion and the presentation, never its composition.
- The system's central concept must sit on the visible layer, not only inside a popup window.
- A public map carries no names of people, projects, servers or paths. Before publishing, read through every text on the map.

## 2. Data

One file, no libraries. The map is data; drawing is a function over it:

```js
const ZONES = [{ id, x, y, w, h, title, d }];               // zones (lanes) with text for the window
const N = [{ id, x, y, icon, name, sub, chips, micro, d }]; // tiles: sub from afar, micro up close, d in the window
const W = [{ pts: [[x,y], ...], color, dash, both, label }]; // wires: a polyline with rounded corners
const STEPS = [{ label, f: [x, y, w, h] }];                 // tour sections: caption and camera frame
```

Canvas 3200×1800. Tiles are absolutely positioned HTML blocks; wires are SVG under the tiles, wire labels are SVG above them with a background sized by `getBBox`. Orthogonal routing with rounded corners, r = 12.

## 3. Camera

- CSS `translate + scale` on the stage, transition 0.5 s `cubic-bezier(.16,1,.3,1)`.
- `fit()` shows the whole map; `frame(rect)` shows a section frame clamped to the edges; `zoomAt(x, y, k)` within 0.7·k0 to 6·k0.
- Wheel pans, Ctrl/Cmd + wheel and pinch zoom, drag with a 5 px threshold, double click zooms ×1.8.
- **Semantic zoom:** at k/k0 ≥ 1.6 the `.zin` class hides `sub` and shows `micro` in a thin weight (Manrope 300). One canvas then holds both the whole picture and the details.

## 4. Focus and window

- A section or a click highlights the nodes inside its frame; everything else dims to 0.2 (nodes) and 0.1 (wires).
- The detail window opens beside the tile and never leaves the screen.
- Keys: ← → and Space — sections, 1–9 — section, Esc — close the window or show the whole map, + − — zoom, 0 or F — whole map.
- Bottom bar: section dots, section caption, zoom buttons, a link to the map PNG.

## 5. Text

- It is a whiteboard, not a document: the map carries the image and text in fragments; details go into zoom and the window. Remove hedging.
- Annotations use a thin weight.
- Wire color carries meaning (flow, error, repair, approval); the legend sits right on the canvas.

## 6. Readability

- In the console `__check(1.08)` and `__zin(true, 1.08)` return an empty list: with text 8% larger no tile breaks. Without that margin, chips that fit "by 0.1 px" wrap onto two lines in another browser.
- On a phone the bottom bar is one line; layouts are in `responsive.md`.
- `?shot=1` gives a clean 3200×1800 frame for the map PNG.

## 7. Inside a talk

Embed the map with `iframe scheme.html?embed=1`; the talk forwards its keys to it through `postMessage` and opens and closes the map itself.

## 8. Publishing

- Vercel: `vercel.json` with noindex and `max-age=300`, fonts `immutable`. Or Cloudflare Pages (→ `publish.md`).
- A password on the whole site is an Edge middleware with a 30-day cookie and the password in an environment variable; add it only when the user asks, and remove it only when they ask.

## Pitfalls

| Symptom | Cause | Right step |
|---|---|---|
| "Cognitive load 10 out of 10" | more text on the map than map | text in fragments, details in zoom and the window |
| The central concept is not visible | it lives only in windows | move it to the visible layer |
| A tile wraps onto two lines in another browser | width without a margin | an 8% margin and `__check` |
| A beautiful map copied from a reference is rejected | the composition does not follow the real process | draw from the code, take only motion from the reference |
| Names and servers on a public map | working data in labels | clean them out before publishing |
| The map and its copy drifted apart | two copies in different places | one map in one place |
