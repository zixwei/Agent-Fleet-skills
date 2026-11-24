# Checking by eye

Done means you saw it on a frame of the right size. A fix "by reading the code" without a frame is not done. Check in batches: capture everything, find every defect, fix, capture again.

## A frame by URL

Any moment of a film, any slide or any map view opens by address, with no clicks:

- film: `?s=<scene>&t=<seconds from scene start>&paused`, plus `&clean` for no panels;
- deck: `#<slide number>` or `?slide=N`;
- map: `#<node>` or a jump parameter.

Add these parameters to the engine from day one: both checking and video recording rely on them.

## Capturing

Use a scriptable headless browser (for example agent-browser). One session per task, a local server on a free port.

```bash
S=<session-name>; B=http://127.0.0.1:<port>/index.html
agent-browser --session $S set viewport 1600 900
agent-browser --session $S open "$B?s=4&t=6&paused&r=$RANDOM"   # r bypasses the page cache
sleep 1.5
agent-browser --session $S screenshot shots/s4_t6.png
agent-browser --session $S console | grep -iE "error|uncaught"
```

- `set device` can set an odd window size; for a phone `set viewport 390 844` is more reliable.
- After changing CSS or JS, bump `?v=NN` in `index.html`, otherwise the browser shows the old version and you fix what is already fixed.
- Clicks: `agent-browser --session $S eval "document.querySelector('#bList').click()"`, then wait 0.6–2 s for the animation.

## Reviewing in a batch

```bash
magick montage -label "" a.png b.png c.png -tile 4x1 -geometry +8+0 -background '#999' all.png
```

`-label ""` is mandatory: without it `montage` fails looking for a font for labels. Shrink large frames first (`magick in.png -resize 50% out.png`).

## Frame checklist

- [ ] Nothing overlaps: text on text, caption on scene, scene card on gradient, buttons on the QR.
- [ ] Nothing is clipped by the screen edge or the frame window.
- [ ] No word breaks across two lines; on desktop the caption is one line.
- [ ] Every number in the frame has a noun.
- [ ] Arrows: the line draws before the arrowhead, one direction.
- [ ] The font is your own (not the fallback), and non-Latin glyphs render evenly.
- [ ] No errors in the console.
- [ ] The QR reads: `zbarimg -q shot.png` prints the right address.

## Sizes

Desktop 1600×900 and 1280×800; tablet 768×1024 and 1024×768; phone 375×667, 390×844, 430×932, 667×375, 844×390. For a deck, also the PDF print.

## After checking

- Show the user the frames or the live address in one question, recommended option first; wait for "yes" before the next batch.
- Clean up after yourself: your frames, the browser session (`agent-browser --session $S close`), your own server by its pid. In zsh, `rm` with a glob that matches nothing fails entirely: delete by explicit names or use `setopt null_glob`.
