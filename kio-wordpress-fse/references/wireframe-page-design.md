---
name: wireframe-to-page-design
description: Design one page of an existing WordPress site from a low-fidelity wireframe/sketch (grey boxes, placeholder frames, developer notes). Extracts structure and copy from the wireframe, then designs the sections with the site's already-shipped design system instead of copying the sketch. Covers only the wireframe-source-specific analysis and design pass; environment, blocks, editor parity, seeding and QA come from the wp-site-pipeline skill. Use when the site already exists and the user sends a wireframe/sketch for a single page.
version: 2.0.0
author: Kio System
---

# Wireframe → Page Design

This skill owns **S1 (analyze) for a wireframe source, in page scope**. It is the second-pass
companion to `image-to-wordpress-blocks`: the homepage was built from a real design image, the
remaining pages arrive only as sketches, and their look must be derived from the site itself.

**Read `.qoder/skills/wp-site-pipeline/SKILL.md` first.** Do not re-derive block file structure,
editor parity, seeding or QA mechanics here.

| Need | Where |
|---|---|
| Stage order for page scope (P-flow), gates, agent routing | rule `wp-build-pipeline` |
| theme.json, SCSS architecture, responsive tiers | `wp-site-pipeline/references/foundation-and-css.md` |
| Block file contract, MediaUpload edit.js, editor parity | `wp-site-pipeline/references/blocks-and-editor-parity.md` |
| Images, page update, media map | `wp-site-pipeline/references/content-seeding.md` |
| QA procedure | `wp-site-pipeline/references/qa-loops.md` |

## Core philosophy

**A wireframe is a content spec, not a visual spec.** Grey boxes, dashed frames, flat placeholder
colors and default fonts are notation — never reproduce them. Extract structure + copy, then design
each section so the finished page looks like the same designer made it as the reference page.

Acceptance is therefore **consistency with the site**, not pixel-diff against the sketch.

## Preconditions

The page must already exist (usually blank, created during the site build) and S0–S5 of the site must
be PASS. If the page does not exist yet, it is a page-plan gap — add it to `docs/build/block-map.md`
first. Never recreate an existing page; only its `post_content` changes.

## 1. Read the wireframe

1. **Sections** top to bottom, keeping the sketch's own label (e.g. `S1 · Brand Story`).
2. **Copy** transcribed **verbatim** — headings, body, quotes, card titles, button labels. Supplied
   copy is client copy; never rewrite, never translate, never "improve".
3. **Placeholders**: every dashed/empty frame and what it is meant to show ("store / jade lifestyle
   image") — each needs real imagery.
4. **Developer notes panel**: an annotation sidebar is documentation, **never page content**. It
   drives scope:

   | Flag | Action |
   |---|---|
   | `Required` | Ships in this pass |
   | `TBD` | Asset/data pending → ship a fitting generated or existing image, expose as a `MediaUpload` attribute so admin can replace it |
   | `Optional` | Skip unless trivial; report it as deferred |
   | `Phase 2` | Do not build; report it as deferred |

5. **Nav elements** (breadcrumb, back link): design cues only — include only if the site already uses
   that pattern.

## 2. Audit the design system before designing

Read what already shipped, and quote it in the spec:

1. Tokens in `src/scss/_tokens.scss` — palette, font stacks, spacing/radius scale.
2. Recurring components in `src/scss/_components.scss` — buttons, cards, section title bars, hover
   effects, section padding scale.
3. Existing `render.php` files — markup patterns and class-naming convention.
4. Available imagery — theme `assets/img/` plus the Media Library map option.

If a value is not in the site's system, it does not go on the page.

## 3. Design pass — the actual added value

For each wireframe section, decide an elevated on-brand treatment:

- **Rhythm**: alternate section backgrounds the way the reference page does; reuse its padding scale.
- **Typography**: map the sketch's text roles onto the existing type scale. **No new fonts.**
- **Placeholders → real imagery**: pick a fitting existing asset, or generate one matching the site's
  photo style; it goes through the Media Library like every other image.
- **Icons**: inline SVG line icons in palette colors, not raster placeholders.
- **Enrich, do not invent**: small flourishes (numbered accents, dividers, a short CTA lead-in) are
  allowed; substantive marketing copy the client did not supply is not.
- **CTAs**: existing button classes, links pointing at real slugs.

## 4. Deliverables

Write `docs/build/page-{slug}-design.md`:

| Wireframe section | Block name (`ai-zippy/{page}-{section}`) | Layout | Background | Type roles (from tokens) | Imagery source | Editable attributes | Flag |

Plus, per section, 2–4 lines of design intent stating which existing component/pattern it reuses, and
the verbatim copy. Then append the page's rows to `docs/build/block-map.md` so the block builder and
seeder work off the same table as the rest of the site.

## 5. Gate for this stage

- Every `Required` section present, in wireframe order, with copy verbatim.
- Every color / size / radius / spacing value traced to a token or an existing component.
- Every placeholder resolved to a concrete filename (existing or to be generated).
- `TBD` items marked with the attribute that will make them admin-replaceable.
- Deferred `Optional` / `Phase 2` items listed explicitly for the user.

## Critical rules

1. Never copy wireframe styling — grey boxes and default fonts are not design.
2. Supplied copy is verbatim.
3. The notes panel is never rendered.
4. No new fonts, no off-palette colors, same class convention, same section rhythm.
5. Same block tech as the rest of the site: server-rendered blocks, one SCSS partial per section,
   images from the Media Library, attributes only for client copy and TBD assets.
6. Update the existing page; do not create a duplicate.
7. Report deferred scope.
