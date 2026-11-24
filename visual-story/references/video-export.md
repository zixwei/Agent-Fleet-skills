# Exporting a film to MP4

A film is a pure function of time (→ `motion-film.md`, section 3), so video is written frame by frame: seek to `t`, draw, capture the frame, assemble with ffmpeg. Real-time screen recording (`agent-browser record`) is good only for a draft: it drops frames and the sound drifts away from the picture.

## What the page must have

- **A `?render=1` recording mode.** Panels hidden, the frame fills the canvas, the `requestAnimationFrame` loop does not run, `localStorage` is not written; theme, language and camera come from the URL.
- **`window.__FILM`** with `seek(t)`, which draws frame `t` **synchronously** (canvas and caption), `T_END` and a `ready` flag. Set `ready = true` after `await document.fonts.ready` and text measuring, with a 4 s fallback timeout.
- **No `Math.random` and no `Date.now` in a frame.** Compute grain, noise and shake from `t` with a fixed seed (`floor(t·24)`), otherwise neighboring frames jitter.
- **`<!doctype html>`.** A fragment page without a doctype opens in quirks mode and the layout shifts.
- **Scale from the frame.** Text uses `--u = min(width, height) / 1080 px` and `calc(var(--u) · N)`: one film renders in 16:9, 1:1, 4:5 and 9:16 without edits.

## Recording with a headless browser

Use a scriptable headless browser (for example agent-browser). A batch of commands captures the frames (`batch` reads JSON: an array of `[command, argument]` pairs).

```bash
S=<session>; OUT=<frames-folder>; mkdir -p $OUT
agent-browser --session $S set viewport 1920 1080
agent-browser --session $S open "http://127.0.0.1:<port>/index.html?render=1"
agent-browser --session $S wait 3000      # or poll window.__FILM.ready via eval
python3 -c "
import json; fps=30; t0=0; t1=<seconds>
c=[]
for i in range(int((t1-t0)*fps)):
    c += [['eval', f'__FILM.seek({t0 + i/fps})'], ['screenshot', f'$OUT/f{i:05d}.png']]
print(json.dumps(c))" | agent-browser --session $S batch > /dev/null
ffmpeg -y -loglevel error -framerate 30 -i $OUT/f%05d.png \
  -c:v libx264 -preset slow -crf 16 -pix_fmt yuv420p -tune animation -movflags +faststart film.mp4
```

- Capture speed is ~5 frames per second: 2 minutes of film at 30 fps take about 12 minutes. Cut a long film into chunks by scene, record chunk by chunk, then join them with `ffmpeg -f concat`.
- Save frames as `.png`: `screenshot` writes PNG even with a `.jpg` extension, and ffmpeg then fails with "No JPEG data".
- Frames are bulky: put the folder on a disk with room and delete it after assembly.
- Mix sound in as a second input: `-i audio.wav -c:a aac -b:a 192k -shortest`.

| Parameter | Value | Why |
|---|---|---|
| Frame | 1920×1080, 30 fps; 60 fps for fast fly-overs | smoothness against recording time |
| Formats | 16:9, 9:16 (1080×1920), 1:1, 4:5 (1080×1350) | social networks and vertical screens |
| `-pix_fmt yuv420p` | mandatory | otherwise some players show a black frame |
| `-tune animation -crf 16` | for flat graphics | no banding on fills |
| `-movflags +faststart` | for the web | the video starts before it fully loads |

## From a click-through film to a video

- Recording time is the film's total time from 0 to `T_END`: scenes back to back, with the camera flights between them.
- Speed is 1×. The 0.5× default is only for a viewer in the browser.
- The viewer camera is off: only the film camera.
- If the video needs pauses between scenes, add them to the timeline (the scene's `dur`).

## Frames first

Before the full recording, capture 4–6 moments (→ `qa.md`) in the target frame format and show them. Record in full after the "yes".

## Sound

- The user decides what is heard: silence, music, event sounds or a voice. Voice and music from paid services only after their "yes" and a budget.
- Compute the event-sound score from the same timeline: `OfflineAudioContext` at 48 kHz renders a WAV, which is mixed in by ffmpeg.
- In the browser, sound is off by default and turns on with a click: browsers block autoplaying sound.
- At speeds other than 1×, divide sound durations by the speed, otherwise the sound lags or stays silent.

## Preview for a page

30 fps, 960 px wide, no sound; step `crf` from 26 to 40 by 2 until the file is ≤ 2 MB. The poster is the final card's frame, 1280 px wide, `-q:v 4`.

### Animation for a README or a page without video

GitHub does not play repository videos inside a README, so the loop is an animated WebP, not a GIF: 3–4 MB GIFs sit as empty cells while they load.

- Capture fresh PNG frames (`seek` + `screenshot`) instead of converting an existing GIF: palette dithering adds noise, and a WebP made from a GIF comes out twice as heavy.
- `ffmpeg -framerate 12 -i f%04d.png -vf "scale=800:-1:flags=lanczos,format=yuv420p" -c:v libwebp_anim -q:v 60 -compression_level 6 -loop 0 loop.webp`: 800 px for a half-width cell, 960 for the main loop; `yuv420p` drops the empty alpha channel.
- Budget: main loop ≤ 600 KB, a cell loop ≤ 1 MB, the whole page ≤ 3 MB. A camera flight changes every pixel of the frame, so cut such loops to 8–10 s.
- README header: a generated banner 1800 px wide with rounded corners in alpha, WebP ≤ 60 KB; under it one row of shields.io `for-the-badge` badges in the project colors; skip the star counter while it reads "0". The social preview is a 1280×640 JPG ≤ 200 KB, bright, with the name large.

## Pitfalls

| Symptom | Cause | Right step |
|---|---|---|
| Pills and captions wider than their text in the first frames | the frame was captured before the font loaded | `ready` only after `fonts.ready` and measuring |
| All frames are identical | `seek` only sets the time and the next `requestAnimationFrame` draws | `seek(t)` draws synchronously |
| The frame "shivers" on a pause | random noise | noise and grain from `t` with a fixed seed |
| The recorded layout differs from the browser | a fragment without a doctype | a wrapper with `<!doctype html>` |
| A visible seam when the film loops | looping from zero | loop from a point that matches the final frame (for example from 1.8 s) |
| The video does not play in some players | the wrong pixel format | `-pix_fmt yuv420p` |
| A viewer with "reduce motion" sees flicker | no `prefers-reduced-motion` branch | open paused on the final frame |
