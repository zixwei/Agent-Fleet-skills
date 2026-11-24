---
name: hooks
description: Write the first two seconds, the on-screen hook text, the title and the cover text for a GTA 6 stream clip, from its transcript and what happens on screen, and optionally add the hook card as an editable moment in the AI Producer project. Use when the user asks for hooks, titles, cover text, or "make the start hit harder".
argument-hint: "[clip or project]"
---

# Hooks, titles, cover text

A clip lives or dies in its first two seconds. The hook is the first spoken line or action plus, when it helps,
three to six words on screen. Read `gtavi/LEARNED_RULES.md` first if it exists: hook types that win on this
page come first.

## Inputs

- The moment's transcript (`scan/transcript.jsonl` around the cut, or `get_transcript` on the AI Producer project).
- What is on screen (the frame sheet from `vodcut verify` or `cut-and-frame`).
- The streamer, the platform, and any campaign rule about titles or hashtags.

## Hook types that fit stream clips

| Type | On-screen text (3-6 words) | Works when |
|---|---|---|
| Stakes | "5 STARS IN 30 SECONDS" | something escalates fast |
| Question | "WHY IS HE IN THE RIVER?" | the payoff answers it |
| Name + action | "SPEED MEETS THE COPS" | the streamer is the draw |
| First | "HIS FIRST GTA 6 HEIST" | a launch-week first |
| Reveal | "WAIT FOR THE CAR" | the payoff is late but huge |
| Quote | the streamer's own line, in quotes | the line is the moment |

Rules:
- The hook text says what happens, never something that does not. No fake "official", no fake collabs, no
  "Speed reacts to [your product]", no tragedy bait.
- Never claim the streamer endorses anything. Name them as the subject, credit them in the post.
- 6 words at most on screen, inside the safe area (8% from the sides, clear of the top 6% and bottom 14%).
- Cut the dead start before writing a hook: if the first second is setup, move the in point (`pick-moments`).

## Deliver

For each clip, three options, best first:

```markdown
### m03
1. on screen: "<3-6 WORDS>"   first line: "<the streamer's actual first words, from the transcript>"   (type: reveal)
   title (Shorts, <=70 chars): <Streamer> <does what> in GTA 6
   cover text: <2-3 WORDS>
2. ...
```

Quote only words that are in the transcript. If the line is unclear in the audio, do not quote it.

## As a moment in AI Producer (optional)

The hook text can be an editable hook card from 0 to about 1.5 s: one composition under the composition contract
(`aip:aip-composition`), static text the editor can retype, on its own track, placed below the facecam band
(`seam_y` + 26 px) so it never covers the face. The official default direction avoids decorative titles; a hook
the user asked for is their instruction and outranks it. Stage it in the same commit as the captions.
