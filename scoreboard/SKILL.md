---
name: scoreboard
description: Track every posted GTA 6 clip (platform, format, hook, caption look, length, campaign, views at 1h/24h/7d, approval, actual payout) and turn the numbers into LEARNED_RULES.md, the rules pick-moments and hooks read next time. Use when the user posts a clip, has new view counts, or asks what is working.
argument-hint: "[add | update | report | rules]"
---

# Scoreboard and learning loop

One CSV, `gtavi/board.csv`, one row per post. Nothing here reads any platform account: the user gives the
numbers, or the public view count of a post they link (read-only).

```bash
S="${CLAUDE_SKILL_DIR}/../../scripts"   # set it in the same command as the calls below
python3 -I "$S/scoreboard.py" init gtavi/board.csv
python3 -I "$S/scoreboard.py" add gtavi/board.csv --clip-id m03 --streamer "IShowSpeed" --platform tiktok \
  --format single --hook stakes --caption-look pop --length 37 --url "<post url>" --campaign "<campaign or empty>"
python3 -I "$S/scoreboard.py" update gtavi/board.csv m03 --views-1h <n> --views-24h <n>
python3 -I "$S/scoreboard.py" update gtavi/board.csv m03 --approved y --payout <amount the campaign actually paid>
python3 -I "$S/scoreboard.py" report gtavi/board.csv
python3 -I "$S/scoreboard.py" rules gtavi/board.csv --out gtavi/LEARNED_RULES.md
```

Shell variables do not survive between separate commands: keep `S=...` and the call in one command, or write the
full `${CLAUDE_SKILL_DIR}/../../scripts/scoreboard.py` path.

Vocabulary, so groups line up: `--format` single | countdown | reaction | explainer | ranking;
`--hook` stakes | question | name-action | first | reveal | quote; `--caption-look` pop | karaoke | single | plain.

## The loop

1. Log every post when it goes up, then views at about 1 h, 24 h and 7 days.
2. After every 10 posts (or every evening on launch week) run `report` and `rules`.
3. `LEARNED_RULES.md` lists what beats this page's own median by 1.5x or more ("do more"), what falls under
   0.67x ("do less"), and campaigns under 50% approval ("fix before submitting"). A rule needs 3+ posts behind it.
4. `pick-moments` and `hooks` read it before choosing. Delete rules that stop holding.

## Honesty

- `payout_usd` is what a campaign actually paid, typed by the user. Never estimates, never "potential".
- Report medians, not the best post. One hit is luck until it repeats.
- Do not share the board's payouts in public copy: they are this user's results, not a promise to anyone.
