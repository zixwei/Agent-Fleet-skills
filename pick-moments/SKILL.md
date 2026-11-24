---
name: pick-moments
description: Choose which scanned GTA 6 stream moments become clips, with a scoring rubric (hook, payoff, reaction, stands alone, readability, novelty, fit) and hard brand-safety and policy checks, then set exact in/out points. Use after vodcut, or whenever the user asks which moments are worth posting.
---

# Pick the moments

Input: `gtavi/runs/<slug>/scan/moments.md`, the frame sheets in `verify/`, and `gtavi/LEARNED_RULES.md`
if the scoreboard has written one (read it first: it is this user's own data and outranks taste).
Output: `gtavi/runs/<slug>/picks.md`, one block per pick, best first.

## 1. Hard no: drop the moment, whatever it scores

- slurs or hate, harassment of a real person, doxxing, anyone who looks like a minor in a risky situation
- sexual content (strip-club and lap-dance scenes included), even in the background of the frame
- an ending, a final mission or a major story reveal; a cutscene shown on its own; an in-game TV show or
  performance on its own (Rockstar's video policy removes these)
- footage from before the New Zealand unlock (11:00 UTC Nov 18, 2026), leaked footage, or anything that
  promotes region tricks, VPNs, cheats or mods
- gambling, crypto or cannabis promotion on screen or in the brief
- real-world violence talk, medical emergencies, or the streamer's private life

Flags from the scan help but do not decide: `profanity` (bleep it if the brief wants it: `cut-and-frame`),
`sexual`, `spoiler`, `loop` (whisper hallucinated over music: listen before trusting the text).

## 2. Score what is left (0-3 each, 21 max)

| | 0 | 3 |
|---|---|---|
| Hook | nothing happens in the first 2 s | something is said or happens in the first second |
| Payoff | it fizzles | a clear peak: bust, crash, scare, discovery, win or fail |
| Reaction | voice only | face and voice both explode, on camera |
| Stands alone | needs the stream's context | a stranger gets it with the sound off and the captions on |
| Readable | menus, map, loading screens | the action reads at phone size |
| Novelty | seen a hundred times | a launch-week first: first cops, first car, an easter egg, the streamer finding his own name |
| Fit | wrong streamer for the page or the campaign | the page's streamer, the campaign's brief, 20-45 s |

Keep 12 or more. Below 10, only keep it if `LEARNED_RULES.md` says this kind of moment wins for this page.

## 3. Set the cut

- Start on the hook, not on the setup: the first frame should already be the moment, or one beat before it.
- End on the reaction's last word plus a short breath; never mid-sentence.
- 20-45 s for a single clip, 9-13 s per item inside a countdown. Hard cap 90 s.
- Use the sheet and the transcript times. Write in/out as `h:mm:ss.s`.

## 4. Write picks.md

```markdown
## m<NN>  <h:mm:ss.s> -> <h:mm:ss.s>  (<length> s)  score <0-21>
hook: "<the first line or action>"   payoff: "<what the peak is>"
why: <one line: why a stranger would watch to the end>
flags: <profanity at h:mm:ss (bleep) | none>   streamer: <name> (<platform>)
cut: vodcut.py cut gtavi/runs/<slug> --pick <NN> --in-out <NN>=<start>-<end> --facecam X,Y,W,H
```

Then hand off to `cut-and-frame`. When the user only wants a list, stop here.
