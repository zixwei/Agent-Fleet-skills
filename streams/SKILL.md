---
name: streams
description: Find GTA 6 streams and VODs worth clipping (IShowSpeed, Kai Cenat or any streamer) on YouTube, Twitch or Kick, with length, views and live status, read-only. Use when the user asks who is streaming GTA 6, what to clip next, or for a VOD link to scan.
argument-hint: "[streamer or search words]"
---

# Find GTA 6 streams and VODs

Goal: a short list of streams worth a full scan, each with a link `vodcut` can read. Everything here is
read-only: yt-dlp lists public pages, nothing is downloaded, nobody signs in.

Scripts live in `${CLAUDE_SKILL_DIR}/../../scripts/`. Run Python with `python3 -I`.

## 1. Search

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/find_streams.py" search "gta 6 $ARGUMENTS" --n 15 --newest --min-hours 1
```

`--min-hours 1` keeps long streams and anything live. Drop it to see fan clips too (step 3).

## 2. Channel pages

Ask for the streamer's channel if the search misses it, then list the latest VODs:

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/find_streams.py" channel "https://www.youtube.com/@<handle>/streams" --n 20 --min-hours 1
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/find_streams.py" channel "https://www.twitch.tv/<login>/videos?filter=archives" --n 20
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/find_streams.py" channel "https://kick.com/<channel>/videos" --n 20
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/find_streams.py" vod "<one VOD URL>"
```

Never run `yt-dlp -J` (or `--dump-json`) on a day-long VOD: its info JSON can run to hundreds of MB.

## 3. Fan clips are leads

Short, high-view videos about a stream tell you which moments the internet already wants. Search
`"<streamer> gta 6"` without `--min-hours`, read the titles, and note the key phrase of each moment
("is that Hulk", "Watkins Auto Parts"). The `vodcut` skill finds those phrases in the transcript with
`vodcut.py find`, because a loudness ranking misses quiet or very short moments.

## 4. Report

Give the user a table: streamer, platform, length, views, live status, link, and one line on why it is
worth scanning. Prefer, in order:

1. finished VODs (`was_live`): a post-live YouTube VOD can be scanned whole; a live one keeps growing
2. the longest, most-watched launch streams (YouTube splits marathons into parts: scan each part)
3. streams with a visible facecam, so the stacked 9:16 layout has a face to show

Reference points from launch day (YouTube metadata, about 19:55 UTC Nov 19, 2026): IShowSpeed's first
GTA VI stream `qSyo5d-dVzA` ran 23h45m with 63M+ views; Kai Cenat's marathon is split into parts
(`VZqAPdUvqnQ`, `H-4PyJlhI9g`, then a live Part 3). View counts move hourly: re-check before quoting.

## Rules

- Only footage from after the game was out: GTA VI unlocked in New Zealand at 11:00 UTC on Nov 18, 2026.
  No leaked or pre-release footage, and never promote region tricks or VPNs to play early.
- Credit is courtesy, not a license. Nothing here makes a clip yours; see `clip-rules`.
- Next step: `vodcut` with the chosen link.
