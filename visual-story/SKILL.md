---
name: visual-story
description: >
  Visual storytelling for showing complex things to people: a motion explainer film on one big canvas with a camera, scenes and captions; an interactive map of a process or system; a pitch deck with micro-animations, transitions and a static PDF; their phone versions, frame-exact MP4 export, publishing to a subdomain and a QR code. Use when the user asks for a motion explainer, an animated walkthrough of a process, "animate how our system works", an interactive system map to zoom around, a pitch deck with transitions and a PDF, "make a video from this animation", or "make it work on phones" — even if they don't say "motion" or "deck". Do NOT use for plain slide decks without scenes or camera, a single static diagram, landing pages and sales proposals, or AI-generated video.
---

# Visual story

Show complex things as images: scene by scene, large, with motion that explains rather than decorates. The caption says what is happening; the canvas shows it. Every batch of work ends with a frame shown to the user and their "yes".

## What you are making → which runbook to open

| Task | Runbook |
|---|---|
| Explainer film: scenes on one canvas, camera, captions, "full scheme" map | `references/motion-film.md` |
| Interactive map of a process or system with jumps between nodes | `references/scheme.md` |
| Pitch deck with micro-animations, transitions and a PDF | `references/deck.md` |
| Plain conference talk without scenes or camera | use a regular slide deck workflow; borrow the readability rules from `deck.md` |
| Export a film or a map to MP4 | `references/video-export.md` |
| Phone and tablet versions | `references/responsive.md` |
| Publishing, subdomain, password, QR | `references/publish.md` |
| Checking by eye: frames by URL, contact sheets, checklist | `references/qa.md` |
| Taste rules: color, type, motion, density | `references/taste.md` |
| Reference works to copy a skeleton from | `references/examples.md` |

Open `taste.md`, `examples.md` and the matching runbook before the first line of code. Open `qa.md` before the first showing.

Film or map? A film is a story that unfolds in time, where a presenter leads the viewer scene by scene. A map is one big picture the viewer explores on their own. "Animate our process for a webinar" is a film; "a map of the system to look around in" is a map. If unsure, ask one question.

## Order of work for every kind

1. **Goal in the user's words, then questions.** Ask in one batch, recommended option first, and only what is still unanswered: which reference ("like last time"), how it is shown (presenter clicks / autoplay / video / PDF), style (the product's website), language, data (fictional or the user's source), deadline and screen.
2. **Script as a table** — scenes or slides: number, title, one line of meaning. Wait for "yes".
3. **Pilot.** One scene or one or two slides, fully finished: image, motion, captions. Frames → show → wait for "yes". Why: a style found in the pilot carries over to everything else; a style found in scene twelve means redoing twelve scenes.
4. **Batches of 2–3** scenes or slides; show each batch and wait.
5. **Assemble the whole:** navigation, full-scheme map or overview, phone layouts, publishing, QR, PDF or video — whatever was ordered.

**Deadline under one day:** one pilot and one "yes", the rest in one batch; check frames only at the sizes it will be shown at (the broadcast screen, plus phones if viewers get a link).

## Before you say "done"

- [ ] Frames at the showing sizes from `qa.md` are taken and reviewed; defects fixed in one batch.
- [ ] If publishing was ordered: the live address serves the new version (`?v=NN`), and `zbarimg` reads the QR.
- [ ] If a PDF or video was ordered: the file is opened and reviewed page by page or frame by frame.
- [ ] The user has seen the result and answered.
- [ ] Your own screenshots, browser sessions and server processes are cleaned up (stop servers by their pid).

## Common mistakes

- Building everything and showing it at the end → the user changes the style and you redo all of it. Work in batches.
- Making things small: ten tiny labels on the canvas instead of one large idea. Image large, code small.
- Hedging with text: "Scan with your phone" under a QR, "still growing", "you can…", gesture hints. Whatever is clear without words stays without words.
- Decorating: colored accent stripes on card edges, blinking, "just pretty". Motion explains a transition or it does not exist.
- A number without a noun and a moment ("8 cards") → the viewer does not know what they are looking at.
- Checking "by reading the code" without a frame at the right size → on a phone everything falls apart.
