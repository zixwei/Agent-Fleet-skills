---
name: cut-and-frame
description: Download the exact window of a picked GTA 6 stream moment and compose it into a 1080x1920 30 fps clip with ffmpeg (facecam band on top, gameplay below, read from a measured facecam rect), bleep words if needed, and record provenance. Use after pick-moments, or when the user asks to make a moment vertical. AI Producer never reframes 16:9 to 9:16, so this always happens before upload.
argument-hint: "[moment ranks, e.g. 1,3,5]"
---

# Cut and frame 9:16

AI Producer keeps the source's orientation: a 16:9 upload stays 16:9 whatever aspect is asked for. So the
vertical frame is built here, before the clip goes to AI Producer.

Scripts: `${CLAUDE_SKILL_DIR}/../../scripts/`. Clips land in `gtavi/runs/<slug>/clips/`.

## 1. Measure the facecam (once per stream layout)

Streamers move their overlays, so measure on this stream, at a time inside the moment:

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/frame.py" grid gtavi/runs/<slug>/verify/m03.mp4 gtavi/runs/<slug>/grid.png --at 5
```

The 360p preview is fine for a first look, but rects are in the pixels of the file you will compose: measure
on the 1080p window (step 2) or scale by 3 from 360p. Read the PNG: yellow lines every 120 source px from the
top-left, faint lines every 40 px. Zoom in for the exact edges:

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/frame.py" grid SRC.mp4 zoom.png --at 5 --zoom 0,420,480,340
```

Write the rect as `X,Y,W,H`, inside the camera's border. Also pick a `--gameplay X,Y,W,H` region that keeps
chat, alerts, sub counters and the streamer's own logo overlays out of the crop. Example (IShowSpeed's
launch-day layout at 1920x1080): facecam `0,462,388,240`, gameplay `0,90,1920,990`. Re-measure every stream.

## 2. Cut and compose

From a vodcut run (downloads the 1080p window, composes, writes provenance, deletes the raw window):

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" cut gtavi/runs/<slug> --pick 3 \
  --in-out 3=2:13:54-2:14:31.5 --facecam 0,462,388,240 --gameplay 0,90,1920,990 \
  --streamer "IShowSpeed" --handle @IShowSpeed --platform youtube
```

`vodcut.py cut` takes the same layout options as `frame.py` and passes them on: `--layout`, `--top-ratio`,
`--focus-x`, `--divider`. It asks yt-dlp how YouTube serves the VOD first: a post-live VOD still served as
2-second fragments goes through `ytfrag.py`, a processed one through yt-dlp. The downloaded window is named by its
span (`clips/m03_src_8034.00-8071.50.mp4`), so cutting again with a new `--in-out` never reuses an old window.

By hand, for any file (download the window with the line that fits the VOD, then check and compose):

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/ytfrag.py" window <VIDEO_ID> 2:13:54 2:14:31.5 src.mp4   # still 2-second fragments
yt-dlp -f "299+140/bv*[height<=1080]+ba/b" --download-sections "*2:13:54-2:14:31.5" \
  --merge-output-format mp4 -o src.mp4 "https://www.youtube.com/watch?v=<VIDEO_ID>"           # processed VOD
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/frame.py" still src.mp4 check.png --facecam 0,462,388,240 --gameplay 0,90,1920,990 --at 8
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/frame.py" compose src.mp4 m03_9x16.mp4 --facecam 0,462,388,240 --gameplay 0,90,1920,990
```

`ytfrag.py` refuses a processed VOD (`... is a processed VOD (protocol https)`); then use the yt-dlp line.

Always Read the `still` before composing. Layouts:
- `stack` (default): facecam band on top, its height from the camera's shape (28-50% of the frame), gameplay
  below. The JSON prints `seam_y`: keep it, captions and headers use it.
- `inset`: gameplay fills the frame, facecam in a bordered box near the top.
- `full`: gameplay only, for moments where the reaction is heard more than seen.
If the plan notes say the crop "still shows the facecam", pass `--gameplay` or `--focus-x` (to `frame.py` or
`vodcut.py cut`). Clips that are joined later (a countdown) all take the same `--top-ratio`, so the seam stays put.

Output: 1080x1920, 30 fps (AI Producer exports at most 30 fps), H.264 + AAC 48 kHz.

## 3. Bleep if the brief asks for it

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/bleep.py" words m03_9x16.mp4 m03_words.json
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/bleep.py" find m03_words.json
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/bleep.py" apply m03_9x16.mp4 m03_9x16_clean.mp4 --spans <spans_arg from find>
```

AI Producer keeps the voice as recorded, so the bleep has to be in the file you upload.

## 4. Check, record, look

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_prep.py" check m03_9x16.mp4
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_prep.py" record m03_9x16.mp4 --streamer "IShowSpeed" --handle @IShowSpeed \
  --platform youtube --source-url "https://www.youtube.com/watch?v=<id>" --start 8034 --end 8071.5
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/framesheet.py" m03_9x16.mp4 m03_sheet.jpg --cols 6 --rows 1 --width 1800
```

Read the sheet: face in the top band, action in the bottom band, no stream overlay cut in half. (Looking at
your own input before upload is fine; the AI Producer skill only forbids inspecting its rendered output.)
The `.clip.json` keeps the source link, times, credit line and disclaimer: campaigns and takedown requests ask
for them. Next: `aip-project`.
