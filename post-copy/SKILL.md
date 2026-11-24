---
name: post-copy
description: Write the post text for a finished GTA 6 clip on each platform (TikTok, YouTube Shorts, Instagram Reels, X) with the streamer credit, campaign hashtags, disclosure and affiliation lines in the right places. Use when a clip is exported and the user asks what to post with it, or for captions/descriptions/hashtags.
argument-hint: "[clip]"
---

# Copy per platform, with credits

Every post credits the streamer, follows the campaign's brief when there is one, and never implies the
streamer, Rockstar or Take-Two made, approved or sponsor it. Read the clip's `.clip.json` (credit, disclaimer,
source link) and the campaign row in `gtavi/campaigns.md` if the clip goes to a campaign.

## Order of every post

1. Disclosure first, when there is one: if the clip is submitted to a paid campaign or you got anything for it,
   start with "#ad" (or the campaign's required wording) and switch on the platform's own paid-partnership or
   content-disclosure setting. A tag buried at the end or in a comment does not count.
2. The hook line (from `hooks`).
3. The credit line: `🎥 @IShowSpeed (YouTube)`. Credit is courtesy, not a license.
4. Campaign requirements (the streamer's name, a required hashtag such as `#LACY`), exactly as the brief says.
5. 2-4 topical hashtags. No hashtag stuffing.

## Per platform

| Platform | Text | Notes |
|---|---|---|
| TikTok | hook + credit + 3 hashtags, about 150 characters | turn on the content disclosure setting for paid posts |
| YouTube Shorts | title up to 70 characters (100 max) + description with credit, source link, disclaimer | the source link is the VOD with its timestamp |
| Instagram Reels | hook line, credit, hashtags at the end | mark paid partnerships with the platform label |
| X | hook + credit in up to 280 characters | the Paid Partnership disclosure for paid posts |

Disclaimer for descriptions (use the clip's own `disclaimer` field):
"Footage: IShowSpeed on YouTube. Not affiliated with or endorsed by IShowSpeed, Rockstar Games or Take-Two
Interactive."

## Never

- "official", "partnered", "Speed x <brand>", or a streamer's face next to a call to action as if they endorse it
- invented quotes: quote only words in the transcript
- earnings claims ("this clip made me $X", "make $10k/month")
- "made with AI Producer" unless the edit was done in AI Producer (in this workflow it was: the captions and
  export come from the AI Producer project; the cut and the 9:16 framing were made locally)

## Output

`gtavi/posts/<clip>.md`: one section per platform, ready to paste, plus the alt text (one sentence on what
happens in the clip) and the source link with timestamp.
