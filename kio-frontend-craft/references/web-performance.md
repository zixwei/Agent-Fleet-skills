
# ⚡ Kio Master Web Performance & PageSpeed Powerhouse (Desktop & Mobile 95–100)

You are the **Principal Web Performance Engineer** of the Kio Ecosystem. This guide combines the Cloudflare 5-phase web performance audit workflow with battle-tested frontend and backend optimization rules to achieve **90–100 Lighthouse / PageSpeed Insights scores on both Mobile and Desktop**.

---

## 🎯 Target Thresholds (Core Web Vitals & Lighthouse)

Mobile Lighthouse simulates a **Slow 4G network + 4x CPU slowdown**. Passing Desktop is easy; passing Mobile requires strict engineering discipline:

| Metric | Good (Target) | Needs Improvement | Poor (Reject) | Primary Lever |
| :--- | :--- | :--- | :--- | :--- |
| **LCP** (Largest Contentful Paint) | **≤ 2.5s** (Ideal < 1.8s) | 2.5s – 4.0s | > 4.0s | Hero `fetchpriority="high"`, no lazy-load on hero, WebP/AVIF, fast TTFB |
| **INP** (Interaction to Next Paint) | **≤ 200ms** | 200ms – 500ms | > 500ms | Defer JS, yield main thread, avoid heavy DOM/hydration |
| **TBT** (Total Blocking Time) | **≤ 200ms** (Ideal < 100ms) | 200ms – 600ms | > 600ms | Code-splitting, delay 3rd-party scripts, `content-visibility: auto` |
| **CLS** (Cumulative Layout Shift) | **≤ 0.1** (Ideal **0.00**) | 0.1 – 0.25 | > 0.25 | Explicit `width`/`height` or `aspect-ratio` on all media, `font-display: swap` |
| **FCP** (First Contentful Paint) | **≤ 1.8s** | 1.8s – 3.0s | > 3.0s | Eliminate render-blocking CSS/JS, inline critical CSS |
| **TTFB** (Time to First Byte) | **≤ 800ms** (Ideal < 300ms) | 800ms – 1.8s | > 1.8s | Page caching, Redis/Transient cache, optimized DB queries |

---

## 🛡️ Proactive Engineering Rules for 95–100 Mobile & Desktop Scores

Always apply these rules **while writing code** (do not wait until the site is slow to fix it):

### 1. 🖼️ Image & Media Optimization (#1 Factor for Mobile LCP & CLS)
- **Above-the-Fold / Hero Image (LCP Element)**:
  - **NEVER add `loading="lazy"` to the Hero / LCP image.**
  - **ALWAYS add `fetchpriority="high" loading="eager" decoding="async"`** to the primary above-the-fold image.
  - Preload the LCP image in `<head>` if loaded via CSS background, or prefer an actual `<img>` / `<picture>` tag so the browser preload scanner discovers it immediately.
- **Below-the-Fold Images**:
  - **ALWAYS add `loading="lazy" decoding="async"`** to all images below the fold.
- **Zero Layout Shift (CLS = 0.00)**:
  - Every `<img>`, `<svg>`, `<video>`, and `<iframe>` **must** have explicit `width` and `height` attributes (or CSS `aspect-ratio`).
- **Next-Gen Formats & Sizing**:
  - Serve `WebP` or `AVIF` (`< 150KB` for hero desktop, `< 80KB` for hero mobile, `< 50KB` for cards).
  - Use responsive `srcset` and `sizes` (in WordPress, use `wp_get_attachment_image()` which outputs `srcset` automatically instead of raw `<img>` URLs).

### 2. 📜 Eliminate Render-Blocking CSS & JavaScript
- **JavaScript Execution**:
  - Never place synchronous `<script src="...">` in `<head>`.
  - Always use `defer` or `type="module"` on scripts.
  - **WordPress Native Defer (WP 6.3+)**: Register scripts with `'strategy' => 'defer'`:
    ```php
    wp_enqueue_script('theme-main', $src, array(), $ver, array('strategy' => 'defer', 'in_footer' => true));
    ```
  - **Dequeue WordPress Frontend Bloat**: Remove unused WP emoji scripts (`remove_action('wp_head', 'print_emoji_detection_script', 7)`), `wp-embed`, and `dashicons` for non-logged-in visitors.
- **CSS Delivery**:
  - Never use `@import` inside CSS files (creates sequential network waterfalls).
  - Keep page-specific CSS modular and scoped; purge unused utility classes in production builds.
  - For below-the-fold heavy sections, apply CSS rendering isolation to slash Mobile rendering cost:
    ```css
    .section-below-fold {
      content-visibility: auto;
      contain-intrinsic-size: 0 500px;
    }
    ```

### 3. 🔤 Web Font Optimization (Zero FOIT / Zero Font CLS)
- **Limit Font Weights**: Load at most 2 font families and 2–3 weights (or 1 variable font `.woff2`).
- **Preconnect & Swap**:
  - If using Google Fonts, always include `<link rel="preconnect" href="https://fonts.googleapis.com">` and `<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>`, with `&display=swap` in the URL.
  - If self-hosting fonts, use `font-display: swap;` and preload the primary heading/body `.woff2` file.
  - **Never load the Google Fonts stylesheet as a render-blocking `<link rel="stylesheet">`.** Use `<link rel="preload" as="style" href="…" onload="this.onload=null;this.rel='stylesheet'">` plus a `<noscript>` fallback. In the Kio seafood test this alone moved mobile FCP/LCP from 1.8 s to 1.1 s and Performance from 98 to 100.
- **Mobile LCP image:** keep it at or under ~80 KB and give `srcset` 480 / 768 px steps. In the Kio seafood re-test a 380 px hero slot at DPR 1.75 skipped the 640 file and downloaded the 1024 one (184 KB): mobile LCP 3.2 s, Performance 92. Adding a 768 px step at WebP quality ~60 (60 KB) brought LCP to 2.3 s and Performance to 99.
- **Favicon:** always declare `<link rel="icon" …>` (an inline `data:image/svg+xml` icon is enough). A missing favicon is a 404 in the console and costs Best Practices points.

### 4. ⚡ Main-Thread & DOM Discipline (Mobile 4x CPU Slowdown Defense)
- **DOM Size Limit**: Keep total DOM nodes `< 800` (warn at `> 1,000`, strictly `< 1,400`). Avoid unnecessary nested `<div>` wrappers.
- **Hardware-Accelerated Animations Only**: Animate only `transform` and `opacity`. Never animate `top`, `left`, `width`, `height`, or `box-shadow` on scroll.
- **Passive Event Listeners**: Always attach `scroll`, `touchstart`, and `wheel` listeners with `{ passive: true }`.
- **Delay Third-Party Scripts**: Defer GTM, Facebook Pixel, chat widgets, and heavy embeds (YouTube/Maps facades) until first user interaction (`scroll`, `pointerdown`, `mousemove`) or `setTimeout(..., 3500)`.

---

## 🔬 Cloudflare 5-Phase Web Performance Audit Workflow

When asked to audit or optimize an existing page:

```
Audit Progress:
- [ ] Phase 1: Performance trace & Lighthouse Mobile/Desktop run
- [ ] Phase 2: Core Web Vitals breakdown (LCP, INP/TBT, CLS culprits)
- [ ] Phase 3: Network waterfall & render-blocking inspection
- [ ] Phase 4: Accessibility & Best Practices check
- [ ] Phase 5: Codebase & Bundler optimization
```

### Phase 1 & 2: Automated Measurement (Lighthouse CLI / DevTools Trace)
Run Lighthouse for **both Mobile and Desktop** (or inspect via Playwright / Chrome DevTools MCP):
```bash
# Mobile Audit (Simulated Slow 4G + 4x CPU throttling)
npx -y lighthouse <target-url> --only-categories=performance,accessibility,best-practices,seo --output=json --chrome-flags="--headless --no-sandbox"

# Desktop Audit
npx -y lighthouse <target-url> --preset=desktop --only-categories=performance,accessibility,best-practices,seo --output=json --chrome-flags="--headless --no-sandbox"
```
If Chrome DevTools MCP is active, run `performance_start_trace(autoStop: true, reload: true)` and inspect `LCPBreakdown`, `CLSCulprits`, and `RenderBlocking`.

### Phase 3: Network & Payload Audit
- Identify any resource in `<head>` blocking First Paint.
- Flag images > 150KB or missing `WebP`/`AVIF` encoding.
- Verify `Cache-Control`, `Brotli`/`Gzip` compression, and remove unused `<link rel="preconnect">` tags.

### Phase 4 & 5: Codebase & Bundler Fixes
- **Vite / Webpack / Next.js**: Enable production minification (`terser`/`swc`), code-splitting (`React.lazy` / `next/dynamic`), and tree-shaking (avoid full `lodash`/`moment` imports).
- **WordPress / WooCommerce**: Disable WooCommerce cart fragments (`wc-cart-fragments`) on static non-shop pages if unused, optimize autoloaded `wp_options`, and ensure all template images have explicit dimensions.

---

## 📊 Performance Audit Output Format

Always report concrete numbers before and after optimization:

| Viewport | Performance | Accessibility | Best Practices | SEO | LCP | TBT / INP | CLS |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **📱 Mobile** | **95 / 100** | 100 / 100 | 100 / 100 | 100 / 100 | 1.8s | 80ms | 0.00 |
| **🖥️ Desktop** | **99 / 100** | 100 / 100 | 100 / 100 | 100 / 100 | 0.7s | 10ms | 0.00 |
