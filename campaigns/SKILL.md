---
name: campaigns
description: Find live GTA 6 clipping campaigns and check their real terms, read-only - rate per 1,000 views, minimum and cap per clip, pool size, fees, platforms, credit and hashtag rules, deadlines, sponsor category - and show what each actually pays with honest math. Use when the user asks where to submit clips, which campaign pays, or whether a campaign is worth it.
argument-hint: "[streamer or platform]"
---

# Campaign finder (read-only)

Clipping campaigns pay creators for views on approved clips. This skill reads public listings and briefs and
turns them into a table the user can act on. It never signs in, joins, applies, accepts terms, submits a clip or
contacts anyone: the user does all of that on the platform themselves.

## 1. Read the public listings

Use web search and fetch on public pages only, for example the Content Rewards discover feed (payouts run
through Whop), Whop listings, Clipster and AffiliateNetwork explore pages, and agency pages that link their
briefs. Search for "<streamer> GTA 6 clipping", "GTA 6 clipping campaign", "GTA VI pool". Skip anything behind a
login or a bot check: do not try to get around it.

## 2. One row per campaign

| Field | Read it from the brief |
|---|---|
| campaign, organisation, verified? | listing header |
| model | CPM (per 1,000 views), pool (budget split by views) or per post |
| rate, minimum and cap per clip | rate card |
| pool budget and qualifying views | pool rules |
| fee to the clipper | platform terms (Content Rewards: 10% on CPM and pool payouts; Clipster: 5% withdrawal) |
| platforms, footage source, length, geography | requirements |
| credit wording, hashtags, logo rules, submission deadline | requirements |
| sponsor category | flag gambling, betting, crypto, cannabis: skip those |
| snapshot time | when you read it (terms change daily) |

Then the honest numbers, per campaign:

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/campaign_math.py" cpm --rate 1.00 --min 10 --cap 500 --fee 0.10 --views 20000x1 --views 400000x1
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/campaign_math.py" pool --budget 10000 --pool-views 100000000 --views 1000000 --fee 0.10
```

"Views to the first dollar" is the minimum divided by the rate; "views to the cap" is the cap divided by the rate.

## 3. Context to give the user (snapshot, Nov 19, 2026)

- Across 67 live CPM campaigns on the Content Rewards feed, the median rate was about $1 per 1,000 views, and
  90% paid $2 or less.
- Minimums of $2-$35 per clip meant roughly 2,000-100,000 views before a clip earned anything; caps of $35-$500
  stopped earning at 50,000-500,000 views.
- Pools split a fixed budget: a $10,000 pool across 100M qualifying views pays about $0.10 per 1,000.
- About $70K of GTA VI clipping budgets were listed on that one marketplace on launch day (budgets include fees).
- These are snapshots. Re-read every brief before submitting.

Always add: "Illustrative math, not a promise. Most clips earn $0."

## Output

`gtavi/campaigns.md`: the table, the math for the top 3, the rules each clip must follow (feed them to
`post-copy` and `clip-rules`), and the snapshot time. Never write "up to $X a month", "passive", "autopilot" or
"while you sleep".
