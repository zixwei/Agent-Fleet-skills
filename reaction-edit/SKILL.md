---
name: reaction-edit
description: Make a reaction or explainer edit about a GTA 6 moment - the user on camera talking about a streamer's clip (or a game system), with the GTA clip seated beside them - edited in AI Producer, whose home lane is exactly this kind of talking-head video. Use when the user wants to react, explain, rank or comment on GTA 6 moments in their own voice.
argument-hint: "[moment or topic]"
---

# Reaction and explainer edits

AI Producer is built for one person talking to camera. A reaction or an explainer puts the user in that seat
and the GTA moment beside them, which is also what platforms reward: commentary and real editing count as
original, a bare re-upload of someone's stream does not (YouTube's reused-content policy, TikTok's originality
rules, Instagram's reposting rules).

## Formats

| Format | The user says | The GTA footage |
|---|---|---|
| Reaction | their live take on a streamer's moment | the composed 9:16 clip, seated above them |
| Explainer | how a system works (wanted levels, heists, the map) | the moments that show it, each at its beat |
| Ranking | "the 3 best launch-day moments, ranked" | one clip per rank |
| Breakdown | why a clip went viral (hook, timing, reaction) | the clip, paused on beats |

Tools this skill calls (Claude Code names): `mcp__plugin_aip_ai-producer__sign_workspace_upload`, `mcp__plugin_aip_ai-producer__commit_workspace`, `mcp__plugin_aip_ai-producer__frame_speaker`, `mcp__plugin_aip_ai-producer__get_task`, `mcp__plugin_aip_ai-producer__finish_project`.

## Steps

1. **Script.** Write the talk with the user: a first line that is the hook (see `hooks`), one idea per
   sentence, 30-60 s. The streamer's clip is evidence, not the content: the user's commentary carries the video.
2. **Record.** The user films themselves vertically (1080x1920, face lit, phone at eye level, quiet room), one
   take is fine: AI Producer's rough cut removes filler words, restarts and dead air.
3. **Open the recording in AI Producer** with `aip-project` (the recording is the source; `check` must pass).
   The usual rough cut (`balanced`) is right here, unlike gameplay clips.
4. **Stage the GTA clip as a workspace video.** `sign_workspace_upload` for
   `render-engine/public/videos/<stem>.mp4` (ASCII letters, digits, `-`, `_`, `.` only), PUT the bytes, and
   name it in the next `commit_workspace` by `sha256`.
5. **Edit with the official skills.** Follow `aip:aip` for the cut and finish, `aip:aip-framing` for the layout,
   and `aip:aip-composition` for how a user clip is mounted. For video assets the framing skill gives two seats:
   a top/bottom split (the clip above, the speaker in a card below) or a PIP; measure the speaker with
   `frame_speaker` rather than guessing. Hold the clip on screen while the user talks about it; return to the
   full-frame speaker between clips.
6. **Captions** in the GTA look: `aip-captions` (`--look karaoke` reads well over talking heads), placed where the
   framing skill puts the band for each layout.
7. **Credit on screen** while the clip plays: a small lower line "Clip: @<streamer> on <platform>", static text in
   the clip's moment. And in the post (`post-copy`).
8. `finish_project`, deliver the `page_url`; export only on request.

## Rules

- Quote the streamer's clip briefly: the shortest stretch that makes the point, never the whole moment twice.
- No putting the streamer's face next to a product or call to action as if they endorse it.
- No claims about the streamer's life, earnings or intentions the clip does not show.
- Facts in an explainer come from sources the user can point to; say "reportedly" or cut the line otherwise.
