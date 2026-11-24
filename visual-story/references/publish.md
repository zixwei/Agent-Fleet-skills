# Publishing, domain, QR

Publishing to a new address is an outward action: get the user's "yes" on the address first. Redeploying to an address already agreed needs no new question.

## Cloudflare Pages (main path)

You need `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` in the environment; never print their values.

1. **Build a clean folder.** Only what the page needs: `index.html`, CSS, JS, fonts, images. No `shots/`, `.scratch`, video sources.
   ```zsh
   #!/bin/zsh
   set -e; cd ${0:A:h}
   rm -rf .dist && rsync -a --exclude '.*' --exclude shots --exclude renders --exclude tools --exclude 'img/raw' --exclude deploy.sh ./ .dist/
   npx --no-install wrangler pages deploy .dist --project-name <project> --branch production --commit-dirty=true
   ```
   Put this in `deploy.sh` next to the project: every redeploy is one command.
2. **Create the project through the API** if it does not exist. `wrangler pages project create` fails in some versions with "Missing entry-point"; the API is more reliable:
   ```bash
   curl -s -X POST "https://api.cloudflare.com/client/v4/accounts/$CLOUDFLARE_ACCOUNT_ID/pages/projects" \
     -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN" -H "Content-Type: application/json" \
     --data '{"name":"<project>","production_branch":"production"}'
   ```
3. **Your own subdomain.** Attach the domain to the project (`POST …/pages/projects/<project>/domains` with `{"name":"<sub.domain>"}`) and add a CNAME `<sub>` → `<project>.pages.dev` with `proxied: true` in the zone. The certificate is issued within minutes.
4. **Check the live address**, not the deployment address: `curl -s https://<address>/ | grep -o 'engine.js?v=[0-9]*'` shows the new version; then take a frame of the live page (→ `qa.md`).
5. **Hide from search** if the page is for a closed audience: `<meta name="robots" content="noindex, nofollow">`.

## Vercel, subpath, password

- A subpath on someone else's Vercel site (like `/pitch/`): the steps are in `deck.md`, section 10.
- Add a password only when the user asks. On Vercel the tested path is an Edge middleware: a 30-day cookie, the password in a project environment variable (→ `scheme.md`, section 8). On Cloudflare Pages there is no tested recipe here: the options are Cloudflare Access or `functions/_middleware.js` with Basic Auth. Check the chosen path with a frame from a clean browser session, and tell the user it is untested until that check passes.

## Subpath on a site already in use

Before deploying into a subpath, check that it is free: an occupied `/deck/` overwrites the previous deck. Pick a free subpath yourself and tell the user; keep the old deployment for rollback.

## QR

The QR points to the live address of the page. The generator is `segno` through `uvx` (add `--offline` once segno is in the uv cache):

```bash
uvx --offline --from segno segno --error q --scale 12 --border 4 --dark '#17251f' --light '#ffffff' -o qr.svg "https://<address>"
```

If the QR is drawn right on the SVG canvas, take the matrix and draw one `<path>` of squares:

```bash
uvx --offline --from segno python -I -c "import segno; q=segno.make('https://<address>', error='q', micro=False); print('|'.join(''.join('1' if c else '0' for c in r) for r in q.matrix))"
```

- Error correction level Q: the QR is scanned off a broadcast screen, with moiré and glare.
- Quiet zone of at least 4 white modules; dark modules on a white plate, not on the canvas background.
- Draw modules with a 0.6-unit overlap, otherwise thin white hairlines show at the seams.
- QR without a caption: no "scan with your phone", no address as text. A QR explains itself.
- **Check:** `zbarimg -q <frame>.png` prints exactly this address. Check on a frame of the live page.
