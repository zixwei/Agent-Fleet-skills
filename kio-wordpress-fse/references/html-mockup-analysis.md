---
name: mockup-to-wordpress-blocks
description: Turn a static HTML mockup into a WordPress FSE site by cloning it literally — extract the mockup's CSS/HTML/JS into the child theme's SCSS partials and server-side rendered blocks. Covers only the HTML-source-specific analysis; the environment, foundation, editor parity, seeding and QA stages come from the wp-site-pipeline skill. Use when the design source is an .html file.
version: 3.0.0
author: Kio System
---

# HTML Mockup → WordPress Blocks

This skill owns **S1 (analyze) for an HTML source** plus the extraction rules that only apply when
the source is real markup. Everything else is shared.

**Read `.qoder/skills/wp-site-pipeline/SKILL.md` first** — it defines the stage order, the handoff
file, and the reference document for each stage. Do not re-derive environment setup, block file
structure, editor parity, page seeding, or QA from this file; they live there.

| Need | Where |
|---|---|
| Stage order, gates, agent routing | rule `wp-build-pipeline` |
| Local env, DB, docker, wp install | `wp-site-pipeline/references/env-bootstrap.md` |
| theme.json, SCSS architecture, header/footer | `wp-site-pipeline/references/foundation-and-css.md` |
| Block file contract + editor parity | `wp-site-pipeline/references/blocks-and-editor-parity.md` |
| Images, pages, products | `wp-site-pipeline/references/content-seeding.md` |
| Visual + functional QA | `wp-site-pipeline/references/qa-loops.md` |

## Core philosophy

**Clone exactly. Do not redesign, reinterpret, or simplify.** The mockup is the specification; the
rendered WordPress page must be indistinguishable from it.

- HTML in `render.php` is the mockup's markup, structure preserved.
- Every color, spacing value, breakpoint, animation and interactive behavior is taken from the
  mockup's own CSS — never re-invented from memory.
- Anything not in the mockup does not go into the site.

## 1. Read the mockup

Open the `.html` file itself. Never work from a screenshot of it — the CSS is the ground truth.

Inventory:

1. **Pages** — an SPA mockup usually holds several pages in one file (`.page`, `#home`, …).
2. **Sections** per page, in order.
3. `<style>` — the full stylesheet.
4. `<script>` — fade-up observers, toggles, accordions, tabs, sliders, SPA router. Every listener
   that opens/closes/toggles a UI element is one behavior-map row (cart button → right drawer,
   hamburger → menu). Grep `addEventListener|onclick|toggle` so none is missed.
5. Header/footer markup, inline SVG logos, Google Fonts `<link>`/`@import`, image filenames.
6. Forms — fields, required flags, submit target.

## 2. Write the spec artifacts

Produce `docs/build/ui-spec.md` and `docs/build/block-map.md` (contents defined in the
`wp-design` agent). Because the source is real CSS, the spec must **quote actual values**
from the mockup: hex codes, px/rem values, breakpoint widths, `grid-template-columns`. No
approximations.

Block naming: `{page}-{section}` kebab-case, namespace `ai-zippy/`. Sections reused across pages get
one shared block (`cta-banner`).

## 3. Split the mockup CSS into partials (HTML-source-specific)

The old approach of dumping the whole `<style>` block into one file is retired: it makes parallel
block work impossible and hides which rule belongs to which section. Split it, do not rewrite it:

| Mockup CSS | Destination |
|---|---|
| `:root` variables, font stacks, palette | `src/scss/_tokens.scss` |
| Reset, base typography, container/wrapper | `src/scss/_base.scss` |
| Buttons, cards, form controls, badges — anything reused | `src/scss/_components.scss` |
| Rules scoped to one section | `src/scss/sections/_{block-name}.scss` |

Rules while splitting:

- Copy declarations **verbatim**, including media queries; move them, do not "clean" them.
- A section's media queries travel with that section's partial.
- Drop only SPA-routing CSS (`.page { display: none }`) and rules that hide whole pages.
- If a mockup rule targets a selector that will become the block wrapper, style the **inner
  semantic class**, not `.wp-block-ai-zippy-*` (see rule `wp-editor-parity`).
- Every partial must be `@use`d from `src/scss/style.scss`.

## 4. Section HTML → `render.php`

- Paste the literal section markup inside a wrapper carrying `get_block_wrapper_attributes()`.
- Replace SPA navigation with real links: `onclick="goTo('packages')"` → `href="/packages/"`.
  Never leave `href="#"`.
- Text a client would plausibly edit becomes a block attribute rendered through `esc_html()`;
  everything else stays static markup.
- `<img src="images/hero.jpg">` becomes a Media Library lookup — see `content-seeding.md`.
- Keep the DOM shape identical: no extra wrappers, no reordering, no renamed classes.

## 5. Mockup JavaScript

| Mockup script | Destination |
|---|---|
| SPA router (`goTo`) | delete — replaced by real WordPress pages |
| Fade-up / scroll reveal observer | `src/js/child.js` |
| Mobile nav toggle | `src/js/child.js` (hamburger is mandatory — see foundation reference) |
| Behavior belonging to one section | that block's `view.js` |

Port the logic as vanilla JS. `view.js` does not run in the editor, so the no-JS default state must
already look correct (accordion first item open, no permanent `opacity: 0`).

The behavior map (format in the `wp-design` agent) is extracted from this reading: section-scoped
behaviors get that block's `view.js` as owner; header/global ones (mini cart drawer, mobile nav)
get `src/js/child.js`. A listener found in the mockup but missing from the map is a spec defect.

## 6. Then follow the shared pipeline

Hand `ui-spec.md` + `block-map.md` to the pipeline and continue with S2 → S7. The mockup file stays
the reference of truth for the S6 visual loop: render it in the same browser at the same viewport
width and compare section by section.

## Definition of done

Every page matches the mockup at 1440px, 768px and 375px; the Gutenberg editor canvas matches the
frontend; all links and forms work. Font differences are out of scope (see rule `wp-qa-gates`).
