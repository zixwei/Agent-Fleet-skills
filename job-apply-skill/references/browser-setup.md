# Browser setup: the user's own Chrome

The skill drives the user's normal Google Chrome through the **Playwright Extension** (Chrome Web Store). That Chrome is where they are already logged in to LinkedIn, so no separate login browser and no saved sessions are needed.

`S` = this skill's `scripts/` folder. You do every step; the user only clicks in Chrome, pastes one code, and restarts Claude Code once. Speak plainly: "the browser connection", "the code from the extension". One instruction per message, ending with what to type back.

## Full setup (first time, or `browser`)

Run `S/browser.py check` and act on `next`. After each user step, run `check` again and continue with the new `next`, until the connection test passes.

| `next` | You do | You tell the user |
|---|---|---|
| `install_chrome` | nothing yet | "Please install Google Chrome from google.com/chrome, open it once, then type **done**." |
| `install_extension` | `S/browser.py open-store` | "I opened a page in Chrome. Click **Add to Chrome**, then **Add extension**. Then type **done**." |
| `get_token` | `S/browser.py open-token-page` | "I opened the Playwright Extension page in Chrome. Next to the code at the top there's a **Copy** button: click it, then paste here." If the page didn't open: "In Chrome, click the puzzle icon at the top right, then **Playwright Extension**." |
| (user pasted the code) | `S/browser.py save-token "<what they pasted>"` | nothing yet; never repeat the code back |
| `register` | `S/browser.py register` | nothing yet |
| `ready_if_tools_loaded` | see "Are the browser tools loaded?" | |

`save-token` and `register` both end with `restart_needed: true`. Then say, and stop until they come back:

> Almost done. Please close Claude Code and open it again in this same folder (in VS Code: press Ctrl+Shift+P, type "Reload Window", press Enter). Then type **/job-apply** and I'll carry on from here.

Setup and runs are resumable, so the restart loses nothing.

If `save-token` says the code doesn't look right: "That code didn't look right. Please click **Copy** again and paste it here."

### Are the browser tools loaded?

Look for the `jobapply-browser` tools (`mcp__jobapply-browser__browser_navigate` and the rest; search your tools for "jobapply-browser" if they aren't listed). Not there → Claude Code hasn't been restarted since the tool was registered: give the restart message above.

### Connection test

1. `browser_navigate` to `https://www.linkedin.com/feed/`, then `browser_snapshot`.
2. Results:
   - **Feed, profile picture or "Me" menu:** connected and logged in. `S/state.py checkpoint browser '{"ok": true}'`. Tell the user: "Chrome is connected and LinkedIn is logged in. Keep Chrome open while I apply. I work in my own coloured tab group; you can use your other tabs as normal."
   - **Sign-in page:** "LinkedIn opened in Chrome but you're not logged in. Please log in there (I never type passwords), then type **done**." On done, snapshot again.
   - **Error "extension did not connect" / token mismatch:** the code changed (someone pressed "Rotate token") or the extension is in another Chrome profile. Run `S/browser.py open-token-page` and ask for the code again, save it, restart.
   - **A Playwright Extension page asking to choose tabs or "Allow":** the code is missing. Ask for it (`get_token` step).
   - **Chrome not installed / not found:** `install_chrome` step.

## Quick check (before every `start`, and when a browser tool errors)

1. Are the `jobapply-browser` tools loaded? If not: run `S/browser.py check`; if `next` is not `ready_if_tools_loaded`, do the full setup; otherwise give the restart message.
2. Connection test above. Logged out → ask the user to log in (this happens before they walk away, so it's fine to wait for them).

## Notes

- Only one sub-agent drives the browser at a time. Sub-agents use the same `jobapply-browser` tools.
- Never call `browser_close` during a run: it disconnects from Chrome. To tidy up, close extra tabs with `browser_tabs` (action `close`), keeping one tab open.
- The extension only lets the skill see tabs it opened itself (shown together as a coloured tab group). It cannot see or change the user's other tabs.
- Old installs had five separate browsers (`jobapply-login`, `jobapply-w1`..`w4`). `S/browser.py register` removes them; `check` reports any left in `old_browser_tools_left`.
