# The canvas on phones and tablets

A film, map or deck is made for a 16:9 screen 1600 px wide. On a phone held upright the same frame is 4 times smaller; on a phone held sideways there is little height. The task is three layouts, not one squeezed by media queries.

## Three layouts

Choose the layout by the window size (`innerWidth`, `innerHeight`), not by the canvas size: the canvas is already shrunk by panels, and a narrow desktop window would wrongly land in "phone landscape".

| Layout | Condition | What changes |
|---|---|---|
| Desktop | everything else | as designed: panels at the top and bottom, captions in the lower third |
| Phone portrait | width ≤ 760 and height > width | a frame window across the full width; the scene title above it, the caption below it; controls at the bottom under the thumb |
| Phone landscape | height ≤ 520 and width > height | the canvas fills the screen, controls float on top, a thin scrubber at the bottom edge |

Duplicate the same predicate in CSS: `@media (max-width:760px) and (orientation:portrait)` and `@media (max-height:520px) and (orientation:landscape)`. The engine sets `body[data-lay]` for its own calculations.

## Phone portrait

- **Frame window.** Window height = `(width − 24) / 2.1`, not 16:9: shots were composed on a wide desktop screen (≈ 2.4:1), and 16:9 crops the edges of scenes. The engine computes the `TOP` and `BOT` margins so that the window, the title above it and the caption below it sit as a group slightly above center.
- **Veil.** Everything outside the window is covered by a `.win` element with `box-shadow: 0 0 0 1px <line color>, 0 0 0 100vmax <canvas color>` and an 18 px radius. The veil fades out when the viewer drags the canvas or the map is open, and stays during the flight between scenes.
- **Scene title** is permanent and light (no dark card): the number in a green chip, the title at 19 px, "what this part is about" at 13.5 px. Anchor its bottom to the top of the window: `bottom: calc(100% − var(--ft) + 16px)`, so a two-line title grows upward, not onto the frame.
- **Caption** right under the window: `top: calc(var(--fb) + 14px)`, 16 px, full width with 12 px margins.
- **Controls** at the bottom: logo on the left, "back — play (58 px circle) — forward" in the center, "scene list" on the right. Remove speed, zoom, fullscreen and time: on a phone they are noise.

## Phone landscape

- `#app` → `display:block`, canvas `position:absolute; inset:0`, both panels on top with a transparent background.
- Buttons are 38 px circles, translucent white with `backdrop-filter: blur(10px)`; play is a green pill.
- Scene title at 15 px, top left, without the "what this part is about" line. Caption at 14 px on one line above the scrubber. Engine margins: `TOP = 46`, `BOT = 70`.
- Respect the screen notch: `viewport-fit=cover` and `env(safe-area-inset-*)` insets.

## A scene list instead of a strip of numbers

On a phone a strip of 12 numbers does not fit and is too small for a finger. A "list" button opens a bottom sheet: number, title, the "what this part is about" line, the current scene highlighted and scrolled to the center; a "Full scheme" button at the bottom. The sheet slides in over 380 ms with `cubic-bezier(.32,.72,0,1)`, a 38% dim behind it, and closes on the backdrop, the close button and Esc.

## Tablet and narrow window (≤ 1100 px)

The desktop layout, but without speed, zoom, "restart scene", time and the logo label; the scene strip shows only numbers, centered. Otherwise the top panel runs off the edge and titles in the strip get cut to "Ho…".

## Details without which it is not "perfect"

- `#app { height: 100dvh }` — otherwise the iOS address bar eats the bottom.
- `touch-action: manipulation` on buttons and `-webkit-tap-highlight-color: transparent` — no tap delay and no grey flash.
- `touch-action: none` on the canvas — gestures belong to pan and zoom, not to page scrolling.
- No hints for gestures or for rotating the phone: viewers already know. Show key hints only with a mouse (`matchMedia('(pointer: coarse)')`).
- The "full scheme" threshold comes from the frame scale (→ `motion-film.md`, section 7).

## Checking

Frames at 375×667, 390×844, 430×932, 667×375, 844×390, 768×1024, 1024×768, 1280×800 and 1600×900 (→ `qa.md`). Separately: open the scene list, jump to a scene, open the full scheme in portrait and landscape.
