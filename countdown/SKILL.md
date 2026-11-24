---
name: countdown
description: Build the "5 craziest GTA 6 stream moments" countdown - a 60-67 s 1080x1920 compilation of streamer moments (IShowSpeed and Kai Cenat mixed by default) with a title, number cards that dock as corner tags, captions and per-item credits - cut locally and finished in AI Producer as the harness. Use when the user wants a compilation, a top-5, a countdown or "the banger format".
argument-hint: "[streamers, window, angle]"
---

# The countdown format

The prompt below is the whole format, word for word as it is shared. Follow it, and run it with the steps in
the second half.

```text
<inputs>
Ask me for, in one message:
1. The streamers (default: Kai Cenat and IShowSpeed, mixed) and the window: "the first 24 hours of GTA 6", "this week", or the VOD links I already have.
2. The angle for the title, picked from: "5 GTA 6 moments chat lost it", "5 things streamers found on day one", "5 GTA 6 moments nobody saw coming", "5 craziest GTA 6 stream moments".
3. The page it's for and its campaign brief, if any (credit line, hashtags, length rules), so every item follows it.
4. Whether AI Producer is connected (the aip plugin, signed in through /mcp). It's open to a limited set of accounts, so check the sign-in before you cut anything.
If I have no VODs, find the longest finished GTA 6 streams of the last 48 hours and pitch me 3 angles with why each one is hot today. Never copy another channel's order, titles or edit.
</inputs>

<direction>
A 1080x1920, 30 fps Short / TikTok / Reel of 60-67 s, built only from the streamers' own VOD footage. Every item is cut on my machine, stacked 9:16 (AI Producer doesn't reframe) and runs 10-14 s: the page's finished edit of that moment if there is one, otherwise a fresh cut. No voice-over, no AI-generated footage, no stock clips: the streamers' own reactions carry it.
AI Producer is the harness: the joined countdown is one AI Producer project. Its transcript times the captions; the title, number cards and captions are written into its workspace as editable layers; it places the sound effects, exports the MP4 and keeps the editable project link. A 1-minute countdown sits far inside its source limits (30 minutes for most accounts, 3.5 GiB). Give me the project link; export the MP4 only when I ask.
Text system, heavy condensed caps (Anton) with a thick black outline, gold #FFD23F and white:
- Title (0-2.2 s): on screen from the first frame over #5's first beat, each word punching in turn, first word in gold.
- Number card on each item's first 0.8 s: the frame blurs and dims, a gold "#4" slams in over the label in white ("KAI'S GETAWAY BIKE") and the credit small underneath ("@KaiCenat · YouTube"), then it shrinks into a "#4" tag in the top-right corner for the rest of the item.
- Captions: a finished edit keeps its own; a fresh cut gets word-by-word captions around 75% of the height, white, the word being said in gold, other voices in light blue. Never two caption layers.
- Closing question over #1's last 1.5 s: "WHO LOST IT MOST?"
- Or, instead of this text system, apply AI Producer's Game UI Countdown preset (Gaming category: numbered pills, game-UI cards, keyword captions) with /aip:aip-reference-style: its captions, cards and transitions only.
Sound: the streamers' own audio, one library impact as each number lands and a whoosh into each item; no AI-generated sound. Bleep profanity before upload when the page or campaign wants it.
Banned: an intro longer than 2.5 s, a greeting, a channel plug, an item without its credit, more than 3 s with neither a face nor text, a 16:9 shot pasted small, the same moment twice, sexual content, slurs, story spoilers or endings, a cutscene on its own, footage from before release, and any line that says or suggests the streamers made, approved or endorse it.
</direction>

<structure>
Length: 60-67 s: five items counting down from #5 to #1.
0-2.2 s, the hook: the title over #5's first beat, already moving and readable on the first frame (never a black frame).
Every item: the number card on its first frame -> the setup in one beat (2 s at most) -> the moment -> the reaction peak -> out on the last word or the laugh, never mid-sentence. Hard cut with a whoosh into the next item.
Order: #5 a strong opener; #4 and #3 alternate streamers; #2 the biggest laugh or the biggest flex; #1 the moment the internet already clipped most, or the rarest discovery. At least two items from each streamer.
End: #1's reaction plays out under the closing question. No outro card, no "follow for more".
</structure>
```

Tools this skill calls (Claude Code names): `mcp__plugin_aip_ai-producer__list_workspace`, `mcp__plugin_aip_ai-producer__get_workspace_file`, `mcp__plugin_aip_ai-producer__commit_workspace`, `mcp__plugin_aip_ai-producer__list_sound_effects`, `mcp__plugin_aip_ai-producer__place_sound_effects`, `mcp__plugin_aip_ai-producer__finish_project`.

## Run it

Scripts: `${CLAUDE_SKILL_DIR}/../../scripts/`. Work in `gtavi/countdowns/<slug>/`.

0. **Connection first.** AI Producer in coding agents is open to a limited set of accounts. Run the sign-in
   and pricing check from `aip-project` (step 0-1) before cutting anything; if the account is refused, stop and
   say so instead of cutting footage that can't be opened.
1. **Moments.** `streams` -> `vodcut` for each VOD -> `pick-moments` with 10-14 s cuts. Credit and platform for
   each. #5 should already be moving in its first frame: the title builds over it.
2. **Items.** For each moment, take the page's finished edit if it has one (the MP4 that `aip-export` saved for
   that project): keep its captions and its look, and choose an in and out that start on a setup beat (the card
   covers the first 0.8 s) and end on the reaction. Otherwise `cut-and-frame` a fresh cut with the same
   `--top-ratio 0.35` for every fresh item, so the band line (`seam_y` 672) never jumps. Bleep there if needed:
   ```bash
   python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/vodcut.py" cut gtavi/runs/<slug> --pick 3 --in-out 3=<start>-<end> \
     --facecam X,Y,W,H --gameplay X,Y,W,H --top-ratio 0.35 --streamer "<name>" --handle @<handle> --platform youtube
   ```
3. **Join.** Write `plan.json` (rank, clip, in, out, label, streamer, platform, credit per item; when a finished
   edit needs two spans, join them into one file first with ffmpeg so it stays one item) and build:
   ```bash
   python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/countdown.py" build gtavi/countdowns/<slug>/plan.json gtavi/countdowns/<slug>
   ```
   `countdown.mp4` (1080x1920, 30 fps, items frame-exact) and `timeline.json` (where each item lands).
4. **Open in AI Producer.** `aip-project` on `countdown.mp4`, prompt: "A 60 s countdown of five GTA 6 stream
   moments, already cut and ordered. Keep the cut exactly as uploaded; add the title, number cards and
   captions." Rough cut stays off: the items are already trimmed.
5. **Title, number cards, closing question.** Fetch the workspace (`aip-captions` step 1), then write the title
   and one header per item at the top of the frame (no `--seam-y`):
   ```bash
   python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/countdown.py" cards gtavi/countdowns/<slug>/timeline.json \
     gtavi/aip/<slug>/render-engine --hosts-out gtavi/aip/<slug>/header-hosts.html
   ```
   Then edit each `compositions/countdown-<rank>.html` timeline into the card: for the first 0.8 s the number
   and label fill the frame over a blur-and-dim filter host, then the number shrinks into the top-right tag and
   the label and credit fade. Add the closing question as one more composition on #1's last 1.5 s, and mount
   everything with `aip_mount.py`:
   ```bash
   python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_mount.py" gtavi/aip/<slug>/render-engine --hosts gtavi/aip/<slug>/header-hosts.html
   ```
   Fresh cuts only: their captions as in `aip-captions` step 3, with `--y 0.75`, mounted after the cards so
   they draw on top. Finished edits already carry theirs. Same font staging, preflight and commit as
   `aip-captions`.
6. **Sound.** `cards` printed `sfx_instants` (each card landing): `list_sound_effects` (family "Impacts &
   Emphasis" for the landings, "Transitions & Movement" for the whooshes), then one `place_sound_effects` call
   with `replace: true`, `anchor: "first_hit"` for the landings and `"peak"` for whooshes that end on the cut.
7. **Hand back.** `finish_project`, give the `page_url`. Export only when asked (`aip-export`).
8. **Post.** `post-copy` with every streamer credited, then `scoreboard add --format countdown`.

Never say a finished edit was made by AI Producer unless it went through an AI Producer project; footage cut
and captioned elsewhere is described as an edit, and AI Producer as the harness that hosts and exports the
countdown.
