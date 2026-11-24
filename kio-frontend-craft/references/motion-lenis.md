# 🌊 Smooth Scroll & Motion with Lenis

Lenis (`lenis` on npm, by darkroom.engineering) smooths wheel/trackpad scrolling while keeping native scroll position, so `position: sticky`, anchors and accessibility keep working. Use it for marketing and storytelling pages, not for dashboards, forms-heavy apps or long data tables.

## 1. Install & base setup
```bash
npm i lenis
```
```css
/* Required base styles — or import 'lenis/dist/lenis.css' */
html.lenis, html.lenis body { height: auto; }
.lenis:not(.lenis-autoToggle).lenis-stopped { overflow: clip; }
.lenis [data-lenis-prevent] { overscroll-behavior: contain; }
.lenis.lenis-smooth iframe { pointer-events: none; }
```
```js
import Lenis from 'lenis'

const reduce = window.matchMedia('(prefers-reduced-motion: reduce)').matches

const lenis = reduce ? null : new Lenis({
  duration: 1.2,
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  smoothWheel: true,
  autoRaf: true,          // Lenis drives its own requestAnimationFrame loop
})
```
- Do **not** combine with `html { scroll-behavior: smooth }` — two smoothers fight each other.
- Touch devices already scroll smoothly; leave `syncTouch` off unless you have a specific reason.

## 2. With GSAP ScrollTrigger (one RAF loop)
```js
import Lenis from 'lenis'
import { gsap } from 'gsap'
import { ScrollTrigger } from 'gsap/ScrollTrigger'
gsap.registerPlugin(ScrollTrigger)

const lenis = new Lenis()                 // no autoRaf: GSAP's ticker drives it
lenis.on('scroll', ScrollTrigger.update)
gsap.ticker.add((time) => lenis.raf(time * 1000))   // ticker time is seconds, Lenis wants ms
gsap.ticker.lagSmoothing(0)
```

## 3. React / Next.js
```bash
npm i lenis
```
```tsx
'use client'
import { ReactLenis } from 'lenis/react'

export function SmoothScroll({ children }: { children: React.ReactNode }) {
  return <ReactLenis root options={{ duration: 1.2, smoothWheel: true }}>{children}</ReactLenis>
}
```
Mount it once in the root layout as a **leaf Client Component** wrapper; the rest of the tree stays Server Components. Access the instance with `useLenis()`.

## 4. Things that must keep native scroll
Add `data-lenis-prevent` to every nested scroll container, otherwise its wheel events scroll the page instead:
- modals, drawers, dropdowns and comboboxes with long lists
- maps (Google Maps, Mapbox, Leaflet), code blocks, tables with overflow
- chat panels, any `overflow: auto` element

Stop page scroll while a modal is open: `lenis.stop()` on open, `lenis.start()` on close.

## 5. Anchors & programmatic scroll
```js
lenis.scrollTo('#pricing', { offset: -80 })   // offset = fixed header height
lenis.scrollTo(0, { immediate: true })        // e.g. on route change
```
Enable `anchors: true` in the options to handle `<a href="#id">` clicks automatically.

## 6. Accessibility & performance rules
- `prefers-reduced-motion: reduce` → do not start Lenis and disable scroll-linked animations.
- Keyboard scrolling (Space, PageDown, arrows) and focus-driven scrolling must still work — test with the keyboard only.
- Animate only `transform` and `opacity` in scroll-linked effects; no layout properties.
- Destroy on unmount/route teardown: `lenis.destroy()`.
- Re-check INP and long tasks in a mobile trace after adding scroll effects (`web-performance.md`).
