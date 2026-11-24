
# 🎭 Playwright MCP — Browser Automation & E2E Testing Engine

A specialized skill for controlling real web browsers via the **Playwright Model Context Protocol (MCP)** server. Enables autonomous AI agents to execute end-to-end tests, capture visual evidence, verify responsive layouts, and audit live web applications.

---

## 🛠️ Core Capabilities & Available MCP Tools

Playwright MCP exposes high-level browser actions to AI agents:

| Tool | Purpose | Key Parameters |
| :--- | :--- | :--- |
| `browser_navigate` | Navigate browser to target URL | `url`, `waitUntil` (`load`, `domcontentloaded`, `networkidle`) |
| `browser_screenshot` | Capture visual snapshot of page or element | `path`, `fullPage` (bool), `selector` |
| `browser_click` | Click on target interactive element | `selector`, `button`, `clickCount` |
| `browser_fill` | Type text into input fields/textareas | `selector`, `value` |
| `browser_hover` | Trigger mouse hover effects/dropdowns | `selector` |
| `browser_evaluate` | Run custom JavaScript in page context | `script` (returns DOM state, computed styles, bounding boxes) |
| `browser_resize` | Resize browser viewport dimensions | `width`, `height` (e.g., 375x812 for mobile, 1440x900 for desktop) |
| `browser_console_logs`| Inspect console messages and errors | Filter by severity (`error`, `warning`, `info`) |

---

## 🚀 Workflows by Agent Role

### 1. 🧪 For `agent-qa` — Automated E2E & Zero-False-Completion
Ensure all features work in a live browser before signing off:
1. **Launch & Navigate**:
   ```json
   { "tool": "browser_navigate", "arguments": { "url": "http://localhost:3000/login" } }
   ```
2. **Interact & Assert**:
   - Fill form: `browser_fill` with email/password test credentials.
   - Click submit: `browser_click` with `button[type="submit"]`.
   - Wait for URL redirect or successful dashboard element.
3. **Evidence Capture**:
   - Take screenshot as permanent proof of test passage (`browser_screenshot`).
   - Check console logs (`browser_console_logs`) to verify **0 uncaught exceptions**.

### 2. 🎨 For `agent-fe` & `wp-qa` — Defensive Spacing & Responsive Verification
Audit against **Zero-Edge-Bleed** across 3 standardized screen tiers:
1. **Tier 1 — Mobile (375px × 812px)**:
   - Run `browser_resize` to `width: 375, height: 812`.
   - Run `browser_evaluate` to verify `document.documentElement.scrollWidth <= 375` (NO horizontal scrollbar).
   - Check container padding: side margins must be >= 16px. Text/cards must never touch screen edges.
2. **Tier 2 — Tablet (768px × 1024px)**:
   - Run `browser_resize` to `width: 768, height: 1024`.
   - Verify multi-column grid transitions (e.g. 2 columns, clean gaps).
3. **Tier 3 — Desktop (1440px × 900px)**:
   - Run `browser_resize` to `width: 1440, height: 900`.
   - Check max-width constraints (`max-w-7xl`), centered alignment, and full-resolution asset sharpness.

### 3. 👑 For `kio` — Pillar 3 Executive Visual Taste Audit
Kio executes visual inspection before final sign-off:
- Capture full-page screenshot (`fullPage: true`).
- Inspect typography pairing, deliberate contrast ratios, and tactile hover states.
- Confirm **ZERO AI Slop**: no generic purple gradients, no broken 3-card bento grids, no misaligned icons.

---

## 📋 Best Practices & Troubleshooting

- **Headless Execution**: Use `--headless` mode on headless servers and CI/CD environments.
- **Dynamic Content Settle**: Always allow network idle (`networkidle`) or wait 300-500ms after navigation/clicks for hydration to complete.
- **Clean Profiles**: Use `--isolated` mode to prevent authentication leaks between independent test runs.
- **Selector Priority**:
  1. `data-testid` attributes (most resilient).
  2. Semantic ARIA roles (`role=button[name="Submit"]`).
  3. Unique CSS IDs (`#submit-btn`).
  4. Descriptive CSS classes (`.btn-primary`).
