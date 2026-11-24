# Reference works

Finished works to copy a skeleton from instead of writing an engine from scratch. All of them are plain HTML, CSS and JS with no build step: open the live page, read its source (view source, or download each file the page loads with `curl`), and start from there. "Like last time" means one of these; if the user's words do not make clear which, ask.

| Kind | Live | What to look at |
|---|---|---|
| Scene-by-scene explainer film: light theme, leaves, full-scheme map, phone layouts, QR | https://memory.iva-agent.com | `engine.js` — camera, captions, the full-scheme map, phone layouts; `kit.js` — easing curves and motion primitives; `s-*.js` — one scene group per file; `film.css` — palette, overlays, the `.win` veil |
| Pitch deck with GSAP transitions and a PDF | https://df.majento.ai/pitch/ | `deck.js` — GSAP scene timelines, `wipe`/`drop`/`iris` transitions, `data-fit`, the `?print=1` mode; `deck.css` — the 1920×1080 stage and slide themes; `vendor/qrcode.js` — a runtime QR |
| Interactive map of a system | https://df.majento.ai/ | `index.html` — the map's data and drawing inline, zones, tiles, wires, tour sections, semantic zoom, `__check` and `__zin` |
| Process film for a client (anonymous, not public) | — | the techniques, described in `motion-film.md` section 7a and `video-export.md`: event tables per layer with `stateOf(events, t)`, actor lanes, tokens moving along wires, an event-sound score, frame-exact MP4 export |

## Taking a skeleton

1. Save the reference page and every file it loads into a new project folder. Delete the previous project's images, data, `shots/` and renders.
2. Remove the previous project's branding: scene texts, logo, palette, special shapes (leaves, brand icons), the QR address. Keep the engine, curves, camera, controls, phone layouts and print mode.
3. Set the new product's palette and fonts (from its website) and reset `?v=` to 1.
4. The first scene or slide is a pilot (→ `SKILL.md`, order of work).

Respect the reference's license and authorship: borrow the techniques and structure, not its content.
