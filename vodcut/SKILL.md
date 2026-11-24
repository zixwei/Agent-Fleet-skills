---
name: vodcut
description: Watch an entire GTA 6 stream VOD locally (audio download, whisper.cpp transcript, loudness and reaction cues) and rank its 30-second windows into distinct moments, then verify the best ones on frame sheets. Use when the user hands over a VOD link or file and wants the best moments found, or says "scan this stream".
argument-hint: "<YouTube id/URL, Twitch/Kick VOD URL or local file>"
---

# vodcut: scan a whole VOD for moments

AI Producer edits a clip; it does not watch a 24-hour stream (its sources are capped at about 30 minutes).
Finding the moments is Claude's job, and this skill does it on the user's machine: no API calls, no upload.

Scripts: `${CLAUDE_SKILL_DIR}/../../scripts/`. Work in `gtavi/runs/<slug>/` in the user's current folder,
one folder per VOD (`<slug>` = streamer + date, e.g. `speed-1118`).

## Needs

`ffmpeg`, `yt-dlp`, `whisper-cli` (whisper.cpp) and a ggml model, found via `--model`, `$WHISPER_MODEL`,
`<repo>/models/` or `~/.cache/whisper/` (`ggml-large-v3-turbo-q5_0.bin`). If one is missing, tell the user
the install line (`brew install ffmpeg yt-dlp whisper-cpp`, then the model download from the whisper.cpp
project) and stop: never fetch a model or a binary yourself without their go-ahead.

## 1. Scan

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" scan "$ARGUMENTS" gtavi/runs/<slug> --jobs 16
```

- Before it downloads, `vodcut.py` asks yt-dlp how YouTube serves the VOD (it prints the answer). Post-live
  VODs still served as 2-second fragments go through `ytfrag.py` (parallel, resumable, signed URLs rotated);
  processed VODs (YouTube processes them later; the launch-day VODs took about a day) go through yt-dlp,
  like Twitch and Kick. A local file is read directly.
- A day-long VOD takes a while: run it in the background and report progress from its log. Re-running the
  same command resumes: finished transcript chunks are skipped.
- Optional, only while the VOD is still served as fragments: start transcribing before the download ends with
  `scan_frags.py <OUT>/audio.m4a.raw.frags139 <OUT>/scan <OUT>/audio.m4a --chunks <hours*3>`.

Outputs in `<OUT>/scan/`: `moments.md` (20 distinct moments, best first, with suggested in/out),
`top_windows.md` (best 150 windows), `windows.tsv`, `transcript.jsonl`, `loudness.tsv`, `run.json`.

Score = loudness z-score of the window's 5 loudest seconds + 0.35 x reaction cues + 0.01 x words.
Whisper repetition loops (it repeats a line over music) are cleaned before scoring and flagged `loop`.

## 2. Read, then add what the ranking missed

Read `moments.md` and skim `top_windows.md`. The ranking finds loud, talky stretches; it misses quiet
discoveries and one-line punchlines. Add moments the internet already clipped (see `streams`, step 3):

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" find gtavi/runs/<slug> "is that hulk|red hulk" --add
```

## 3. Verify on frame sheets

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" verify gtavi/runs/<slug> --top 8
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" verify gtavi/runs/<slug> --rank 21,22
```

This pulls a cheap 360p copy of each moment and tiles 12 frames into `verify/mNN_sheet.jpg`. Open every
sheet with Read and check: does the picture show the payoff the transcript promises? Is the facecam
visible, and where? Anything on screen that rules the clip out (menus only, a cutscene on its own,
sexual content)? Write what you saw into `verify/index.md` next to each moment.

## 4. Hand off

Pass the verified moments to `pick-moments`. Report the run with:

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" report gtavi/runs/<slug>
```

It prints hours scanned, 30-second windows ranked, distinct moments (plus those `find --add` added) and the
wall time; the API cost is $0 because everything ran locally. The wall time is only printed for a folder whose
first scan fetched the audio itself and ran start to finish: a resumed or seeded folder says why it has none.
Quote these numbers as they are printed, never estimated.
Delete `verify/*.mp4` and the audio when the user is done: day-long audio is hundreds of MB.
