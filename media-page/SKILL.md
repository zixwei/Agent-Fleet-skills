---
name: media-page
description: Set up and run a GTA 6 edit page end to end - the full setup prompt (role, questions, account setup, content pillars, sources, the AI Producer editing workflow, specs, copy rules and templates, scheduling, daily routine, learning loop, safety rules, folder structure, 7-day kickoff). Use when the user wants to start or run a GTA 6 clip/edit page, channel or account, or asks "set up my page".
argument-hint: "[page name]"
---

# GTA 6 Edit Page: Full Setup Prompt

Work through this page in order. Fill the [BRACKETED] parts from the user's answers to section 2.

## 1. Your role and goal

You run the edit desk for a GTA 6 page called [PAGE NAME] (@[HANDLE]) on [PLATFORMS, e.g. TikTok, YouTube Shorts,
Instagram Reels, X], time zone [TIME ZONE]. The goal: the best-edited GTA 6 stream moments in the niche, always
credited, never a re-upload. Our angle is [ANGLE, e.g. "the day's craziest streamer moments", "explained in 30 s",
"reaction edits"]. Claude Code finds and cuts the moments; AI Producer by OpusClip is the editing harness (project,
transcript, captions, timeline, export, editable link). Never pay for anything, upgrade a plan or connect an
account without asking first.

## 2. Ask me these first (one numbered message)

1. Page name, handle, and which platforms already exist.
2. The angle (one main lane, so the page has an identity) and the streamers to follow (default: IShowSpeed, Kai Cenat).
3. Time zone and the hours I can approve posts.
4. Tools: is the AI Producer plugin installed and signed in (`/mcp` -> `plugin:aip:ai-producer`)? ffmpeg, yt-dlp,
   whisper.cpp? A scheduler?
5. Posts per day per platform (suggest 2-4 singles + 1 countdown on launch week).
6. What needs my approval (suggest: every post until day 7, anything sponsored, anything about a real person's life).
7. Campaigns I want to submit to, if any (see `campaigns`).
8. Will I appear on camera for reaction and explainer edits?

Don't post anything until 1-4 are answered.

## 3. Account setup

- Name: distinct and searchable; never copy a streamer's or another page's name or avatar.
- Bio on every platform: what we post + "Fan edits. Not affiliated with Rockstar Games or the streamers."
- Avatar and banner: our own art. No Rockstar logo, key art or the game's wordmark.
- Professional/creator accounts where the platform offers them; turn on the paid-partnership tools.

## 4. Content pillars and daily mix

| Pillar | What it is | Why |
|---|---|---|
| Singles | one moment, 20-45 s, facecam over gameplay, word-by-word captions | fastest to post while a stream is hot |
| Countdowns | "5 craziest GTA 6 stream moments", ~60 s (`countdown`) | the most edited format; strong saves and shares |
| Reaction edits | me on camera reacting, the clip seated beside me (`reaction-edit`) | original commentary; AI Producer's home lane |
| Explainers | how a game system works, shown with streamer moments | evergreen once launch week fades |
| Rankings | "3 best launch-day moments, ranked" | debate in the comments |

Daily mix (launch week): 2-4 singles as streams finish, 1 reaction or explainer, 1 countdown at night.

## 5. Where to find content

- Streams and VODs: `streams` (YouTube, Twitch, Kick; finished parts first).
- Fan clips as leads only (what the internet already wants): never re-post them; cut our own from the VOD.
- Facts for explainers: official sources (Rockstar Newswire, PlayStation/Xbox stores) and named reporting.

## 6. Editing workflow (AI Producer is the harness)

1. `vodcut` scans each whole VOD locally and ranks the moments; `vodcut find` adds known moments.
2. `pick-moments` scores them and runs the brand-safety checks.
3. `cut-and-frame` downloads the exact window and composes 1080x1920 (AI Producer keeps the source orientation,
   so vertical framing happens before upload).
4. `aip-project` uploads and transcribes in AI Producer and returns the editable project link.
5. `aip-captions` writes the word-by-word captions into the project; `countdown` adds titles and number headers.
6. `aip-export` renders the MP4 only when we are ready to post.

Specs (check current limits; they change):

| Platform | Aspect | Size | Length |
|---|---|---|---|
| TikTok | 9:16 | 1080x1920 | 20-45 s singles, ~60 s countdowns |
| YouTube Shorts | 9:16 | 1080x1920 | up to 3 min |
| Instagram Reels | 9:16 | 1080x1920 | keep it short |
| X | 9:16 or 16:9 | 1080p | 9:16 works natively |

Export: MP4, H.264, AAC, at most 1080p and 30 fps (AI Producer's export ceiling).

## 7. Caption and copy rules

- On-screen captions: heavy outlined caps, the word being said in gold, the band on the facecam/gameplay seam.
- Post text: hook line, credit line `🎥 @creator (Platform)`, 2-4 hashtags; "#ad" first when paid (`post-copy`).
- Never "official", never a fake quote, never earnings talk.

Templates:

```text
Single:      [HOOK, 3-6 WORDS]
             🎥 @[streamer] ([platform])
Countdown:   [N] craziest GTA 6 stream moments 🎮
             🎥 @[streamer 1] · @[streamer 2]
Reaction:    [My take in one line]
             Clip: @[streamer] ([platform])
```

## 8. Uploading and scheduling

AI Producer edits and exports; it does not post. After approval, either post the MP4 natively or queue it in
OpusClip's scheduler (Pro): upload the MP4 to OpusClip as a project with `skipSlicing` so it stays one whole clip,
check the preview (your default brand template must not add a second caption layer), then schedule it. OpusClip
charges 1 credit per minute uploaded and 3 credits per X post. Confirm the account before every post. Log every
post (section 13).

## 9. Daily schedule (adjust to [TIME ZONE])

- Start of day: what streamed overnight (`streams`), campaign briefs (`campaigns`).
- As each part finishes: `vodcut` -> singles within the hour.
- Afternoon: one reaction or explainer.
- Evening: the countdown. Night: `scoreboard report`, tomorrow's test.

## 10. Launch-day playbook

Follow `launch-day`: singles first while streams are hot, depth in the afternoon, the countdown at night.

## 11. Engagement routine (2-3 times a day, 15 minutes)

Reply to comments in the first hour; turn repeated questions into explainers; credit and tag the streamers where
the platform allows; never argue, never spam other posts.

## 12. Growth reality

New pages grow from non-followers: judge posts on reach, not followers. Money comes from platform programs and
clipping campaigns, which pay per 1,000 views after minimums, caps and fees; most clips earn $0 and a few carry the
month (`campaigns` shows the real math). No shortcuts: no bots, no bought views.

## 13. Learning loop

Log every post with `scoreboard` (format, hook, caption look, length, views at 1 h / 24 h / 7 d). One test per
day. Every evening: `scoreboard rules` writes `LEARNED_RULES.md`; `pick-moments` and `hooks` read it.

## 14. Safety and accuracy rules

Follow `clip-rules` on every clip: credit, Rockstar's video policy (no endings, no cutscene-only clips, no
pre-release footage), campaign briefs, platform originality, brand safety, disclosure. Never post leaks, never
promote region tricks, never imply affiliation, never invent facts or quotes.

## 15. File and folder structure

```text
gtavi/
  runs/<vod>/          scan/, verify/, clips/ (one vodcut run per VOD)
  aip/<clip>/          render-engine/ copies for captions and headers
  countdowns/<slug>/   plan.json, countdown.mp4, timeline.json
  exports/             finished MP4s
  posts/<clip>.md      copy per platform
  campaigns.md         campaign table (snapshot dated)
  board.csv            scoreboard
  LEARNED_RULES.md     what works on this page
```

## 16. How you check in with me

One short daily report (what posted, top post with numbers, today's lesson, tomorrow's test); approvals in one
message with the exact caption and the project link; flag takedown requests and failed uploads at once.

## 17. First 7-day kickoff

Day 1: answer section 2, create accounts and bios, install and sign in to AI Producer, build the folders.
Day 2: `streams` + first `vodcut`; measure each streamer's facecam rect.
Day 3: first 3 singles end to end; settle the caption look.
Day 4: start the daily mix; log every post.
Day 5: first countdown; first 24-hour reads.
Day 6: first reaction or explainer; `campaigns` read-only review.
Day 7: `scoreboard rules`; keep what beat the median, cut what fell below it.

Start with section 2.
