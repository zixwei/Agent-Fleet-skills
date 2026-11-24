---
name: launch-day
description: Hour-by-hour playbook for GTA 6 launch days (and any big stream day) - which streams to scan first, when to post single clips versus the countdown, how many AI Producer projects to run at once, and the evening review - chaining the other skills. Use when the user says "it's launch day", "plan my day", or wants a schedule for clipping a live event.
argument-hint: "[streamers, hours available]"
---

# Launch-day playbook

Launch day is about being early with clean, credited clips, not about volume. The pile of footage is enormous:
per Streams Charts, GTA VI drew a 7.91M peak of concurrent viewers and about 125M hours watched in its first
24 hours. Nobody watches that back; they watch the 30 seconds that matter.

The game unlocked country by country at local midnight on Nov 19, 2026, starting in New Zealand at 11:00 UTC on
Nov 18 (US at 05:00 UTC Nov 19). Every clip must come from after the unlock (`clip-rules`).

## Before the first stream ends

- `streams`: list who is live and which parts are already finished (YouTube splits marathons into parts).
- `campaigns`: today's briefs, read-only. Note credit wording, hashtags and deadlines.
- Measure each streamer's facecam once (`cut-and-frame`, step 1) on a live preview; layouts rarely change mid-day.

## Hours 0-6: singles, fast

1. `vodcut` on every finished part as soon as it ends (background job; it resumes). While YouTube still serves
   a long part as 2-second fragments, start transcribing during the download with `scan_frags.py`; a processed
   part downloads in minutes through yt-dlp, so there is nothing to overlap.
2. `vodcut find` the phrases fans are already clipping; `vodcut verify` the top 8.
3. `pick-moments`: 3-5 singles, 20-45 s, reaction on camera.
4. `cut-and-frame` -> `aip-project` (open them all, then wait) -> `aip-captions` -> `finish_project`.
5. Export only what will be posted now (`aip-export`), `post-copy`, post, `scoreboard add`.
   Submit to campaigns inside their deadline.

Keep at most a handful of AI Producer projects running at once: one task or commit runs per project, and
`wait_task` holds at most 8 waits per caller.

## Hours 6-18: depth

- A reaction or explainer in the user's own voice (`reaction-edit`) about the moment everyone is talking about.
- More singles from the second streamer; mix streamers so the page is not one person's re-upload channel.

## Evening: the countdown

- `countdown`: the day's 5 craziest moments, Speed and Kai mixed, ~60 s, every item credited.
- `scoreboard report` + `rules`: what hooks and formats beat the page's median today. Tomorrow's picks follow it.

## Never

- pre-release or leaked footage, region tricks, VPNs
- bots, bought views, engagement pods, mass duplicate uploads
- reposting another clipper's edit
- "official", collab or endorsement language about the streamers, Rockstar or Take-Two
- earnings claims in posts
