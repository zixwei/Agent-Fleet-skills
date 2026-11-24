# 🧹 Kio 9-Pass De-Slopping SOP (`/unslop`)

Use this to refactor an existing UI that "looks AI-generated": generic gradients, centred everything, emoji icons, buzzword copy, uniform cards. Run the passes **in order** — later passes assume earlier ones are done. After each pass, take a screenshot at 375 px and 1440 px and compare with the previous one.

Before starting, pick a direction with the data engine so the result is not a different flavour of generic:

```bash
python3 "${KIO_FE_ROOT:-skills/kio-frontend-craft}/scripts/search.py" "<product type + mood>" --design-system
```

## Pass 1 — Spacing
- Wrap every section in the Kio container: `max-w-7xl mx-auto px-4 sm:px-6 md:px-8 lg:px-12`. Nothing touches the viewport edge at 375 px.
- One spacing scale only (4/8 px based: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128). Replace arbitrary values (`mt-[37px]`).
- Section rhythm: `py-16 sm:py-24`; clear fixed headers with `pt-20 sm:pt-28`.
- Related items closer together than unrelated ones (proximity), so groups read without borders.

## Pass 2 — Colour (80 / 15 / 5)
- 80% neutral surfaces, 15% secondary/brand tint, 5% accent reserved for the primary action and key data.
- Remove decorative purple→pink→blue gradients and glows. If a gradient stays, it is subtle, two adjacent hues, and has a purpose.
- Define colours as tokens (`--color-bg`, `--color-fg`, `--color-accent`…) from `assets/templates/design-tokens-starter.json`; no raw hex in components.
- Body text ≥ 4.5:1 contrast, large text and UI controls ≥ 3:1, in light **and** dark mode.

## Pass 3 — Icons
- No emoji as UI icons. One icon family (Phosphor, Lucide, Heroicons) at one stroke weight; sizes 16/20/24 only.
- Icons support labels; never icon-only buttons without `aria-label`.
- Delete icons that only decorate a heading.

## Pass 4 — Copy
- Remove buzzwords ("unleash", "revolutionize", "seamless", "next-gen", "supercharge", "game-changing").
- Headline = concrete outcome for a specific person; subhead = how, in one sentence; CTA = verb + object ("Start free trial", not "Get started now 🚀").
- Replace placeholder stats and fake testimonials with real ones or remove them.

## Pass 5 — Typography
- Two families maximum (pairings: `search.py "<mood>" --domain typography`). Body 16–18 px, line-height 1.5–1.7, measure 60–75 characters (`max-w-prose`).
- A real type scale (e.g. 1.25 ratio); headings tighter line-height (1.1–1.25) and slight negative tracking at display sizes.
- Weight creates hierarchy before size and colour do. No more than three weights on a page.

## Pass 6 — Layout (break the symmetry)
- Replace "three identical cards in a row" and "everything centred" with a deliberate grid: 7/5 or 8/4 splits, one dominant element per section.
- Left-align long text. Centre only short, standalone statements.
- Vary section structure down the page (media-left, media-right, full-bleed, dense list) so it does not read as a template.

## Pass 7 — Details
- Consistent radius scale (e.g. 6/10/16) and one shadow scale; borders 1 px with low-contrast neutrals.
- Every interactive element has hover, focus-visible (2 px ring with offset), active and disabled states.
- Forms: 44 px min height, 16 px font (no iOS zoom), visible labels, inline errors next to the field.

## Pass 8 — Visuals
- Real product screenshots or purposeful illustration over stock 3D blobs. Same aspect ratios within a group; reserve space with `aspect-ratio` (CLS = 0).
- Images: AVIF/WebP, `width`/`height` set, `loading="lazy"` below the fold, `fetchpriority="high"` on the LCP image only.

## Pass 9 — Motion
- Motion explains change (enter/exit, reorder, feedback); 150–250 ms for UI, 300–500 ms for larger transitions, ease-out on enter.
- Animate `transform` and `opacity` only. Respect `prefers-reduced-motion` (disable parallax, scroll-jacking and auto-play).
- Smooth scrolling via Lenis per `motion-lenis.md`, never on top of native `scroll-behavior: smooth`.

## Done when
Run the canonical checklist in [`ui-ux/pro-rules.md`](ui-ux/pro-rules.md#pre-delivery-checklist-canonical--the-only-one) and the Core Web Vitals check in [`web-performance.md`](web-performance.md). Report before/after screenshots and the list of changes per pass.
