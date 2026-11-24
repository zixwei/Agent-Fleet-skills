---
name: image-to-wordpress-blocks
description: Turn a design image (screenshot/mockup export) into a WordPress FSE site by measuring the image into a quantified UI spec, extracting text baked into photos, and closing the gap with a vision-model comparison loop. Covers only the image-source-specific analysis; the environment, foundation, editor parity, seeding and QA stages come from the wp-site-pipeline skill. Use when the design source is a .png/.jpg instead of HTML.
version: 3.0.0
author: Kio System
---

# Design Image → WordPress Blocks

This skill owns **S1 (analyze) for an image source** plus the two problems that only exist when the
source is a picture: measuring instead of reading CSS, and text baked into photos. Everything else
is shared.

**Read `.qoder/skills/wp-site-pipeline/SKILL.md` first** — it defines the stage order, the handoff
file, and the reference document for each stage. Do not re-derive environment setup, block file
structure, editor parity, page seeding, or the QA loop mechanics from this file; they live there.

| Need | Where |
|---|---|
| Stage order, gates, agent routing | rule `wp-build-pipeline` |
| Local env, DB, docker, wp install | `wp-site-pipeline/references/env-bootstrap.md` |
| theme.json, SCSS architecture, responsive tiers, banner cover pattern | `wp-site-pipeline/references/foundation-and-css.md` |
| Block file contract, MediaUpload edit.js, editor parity | `wp-site-pipeline/references/blocks-and-editor-parity.md` |
| Images, pages, products | `wp-site-pipeline/references/content-seeding.md` |
| Visual + functional QA procedure | `wp-site-pipeline/references/qa-loops.md` |

## Core philosophy

The image is the specification, but it is not machine readable — so **measure, do not guess**. Every
number that ends up in SCSS must come from a measurement on the image, expressed relative to the
design width. Write no code during analysis.

## 1. Measure the image into a spec

Produce `docs/build/ui-spec.md` covering:

1. **Design width** — the pixel width the image represents (usually 1440 or 1920). Every other
   measurement is recorded relative to it.
2. **Sections**, top to bottom: name, height, inner padding, background type.
3. **Container max-width** and the gutter between container edge and content.
4. **Grid per section**: column count, gap, card aspect ratio.
5. **Typography scale**: size, weight, line-height, color, letter case per text role. Font family is
   a best guess — do not spend iterations on it (see QA scope below).
6. **Palette**: every distinct hex, with its role.
7. **Spacing scale**: the recurring vertical rhythm values.
8. **Images**: filename you will use, position, aspect ratio, focal point.
9. **Alignment and overflow**: what bleeds past the container, what is centered.
10. **Components**: buttons (size, radius, padding), cards, badges, icons, accordions.
11. **Banner vs content classification** per section — this drives the mobile treatment and is the
    #1 cause of broken mobile layouts. Definition and template: `foundation-and-css.md`.
12. **Interaction affordances** — a picture has no JS, so behaviors come from convention. Check the
    image against this list and record every hit as a behavior-map row (format in the `wp-design`
    agent): cart button with a count badge ⇒ mini-cart drawer from the right; hamburger ⇒ slide-in
    menu; two-ended arrows ⇒ slider; chevron ⇒ accordion; magnifier ⇒ search overlay. An affordance
    that is visible but ambiguous is one question to the user, not a guess.

Measurement technique: pick one known reference (the design width) and derive everything as a ratio,
then convert to px at the design width. Cross-check repeated elements (card gaps, section paddings)
against each other — designs are built on a scale, so values should repeat.

## 2. Text-in-image audit (mandatory)

Audit every section for typography baked into a photo. Hero banners, CTA banners, category cards
with labels and story sections almost always have it.

**Text baked inside an image is never shipped as part of the image.**

1. **Detect** — for each image ask: does it contain typography?
2. **Transcribe** — exact wording, line breaks, per-line styling, plus geometry (position, size,
   color, alignment) into the spec.
3. **Clean background** — produce a text-free version:
   - Simple backgrounds (walls, fabric, flat areas): inpaint the text bbox plus padding.
   - Text over complex subjects (hands, faces, products): inpainting smears — generate a replacement
     photo of the same scene with an explicit "no text" instruction and crop to the original ratio.
4. **Overlay as real HTML** — the block renders the clean image plus positioned HTML text and real
   `<a>` buttons, styled in `src/scss/sections/_{block}.scss` to the measured geometry.
5. **Make it editable** — every extracted string becomes a block attribute; the background becomes a
   `MediaUpload`-replaceable attribute (pattern in `blocks-and-editor-parity.md`).

Never use invisible click hotspots over baked-in text as a substitute. The admin must be able to
change wording and swap the background without touching code.

## 3. Page plan from the header

The image is almost always the homepage only.

1. Extract every nav item from the header.
2. Each nav item gets a real page with the right title and slug.
3. Pages other than the one in the image are created **blank** — no invented content, no generated
   copy. They are placeholders so navigation does not 404.

Record this as the page plan table in `docs/build/block-map.md`, together with the section → block
mapping (`{page}-{section}` kebab-case, namespace `ai-zippy/`).

## 4. Then follow the shared pipeline

Hand `ui-spec.md` + `block-map.md` to the pipeline and continue with S2 → S7. Keep the original image
path recorded in `docs/build/BUILD-STATE.md` — it is the reference of truth for S6.

## 5. Vision comparison in the S6 loop (image-specific)

The mechanics of the loop (capture commands, iteration cap, fix routing table) are in
`qa-loops.md`. What is specific here is the comparison step: the reference is a flat image, so use a
vision model rather than a DOM diff.

Prompt shape:

```
Image A: original design. Image B: screenshot of the built site at the same width.
Report every visual difference, in this order:
1. Missing / extra / reordered sections
2. Box geometry: section height, padding, gaps, container width
3. Grid: column count, card size, alignment
4. Colors: background, text, accent, border
5. Images: crop, aspect ratio, focal point
6. Text content and CTA labels
For each difference give: section name, what differs in B, and the likely cause.
Ignore font family, letter-spacing, and glyph width differences.
```

Feed the report into the fix routing table in `qa-loops.md`. Rebuild before every re-capture; never
conclude from code alone.

**Font is out of scope.** Font family cannot be recovered reliably from a raster image, so do not
spend iterations on it. Judge vertical rhythm and box geometry only — glyph width differences of
10–30% at matching pitch are expected, not defects.

## Definition of done

Homepage matches the image at the design width plus 768px and 375px; placeholder pages exist and
resolve; the Gutenberg editor canvas matches the frontend; every extracted string and background is
editable in wp-admin.
