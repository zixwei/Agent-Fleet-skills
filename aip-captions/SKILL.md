---
name: aip-captions
description: Write word-by-word, GTA-style captions into an AI Producer project's workspace (caption moments the user can retype in the editor), timed by AI Producer's own caption_cues.py, placed on the facecam/gameplay seam, checked with the official preflight and committed. Use after aip-project, or when the user wants captions, a different caption look, or bleeped/emphasised words on an AI Producer GTA clip.
argument-hint: "[look: pop | karaoke | single | plain]"
---

# GTA-style captions in the AI Producer workspace

Captions are written as caption moments, the way the official `aip:aip-dynamic-caption` skill defines them:
static text per cue that the editor can select, retype and restyle, on a paused GSAP timeline, timed by
AI Producer's `caption_cues.py`. This skill only adds the look (heavy outlined type, the word being said lit
up, the band on the seam) and the scripts that write it. Read `aip:aip-composition` and
`aip:aip-dynamic-caption` once before the first commit; they own the contract. Never call `build_captions`,
never use the `narrator-captions` id, never write `compositions/narrator_captions.html`.

Our source is already a 1080x1920 composite (facecam on top, gameplay below), so the root speaker video stays
at full frame (the `speaker` register in `aip:aip-framing`): no seat or overlay is needed for captions.

Paths: this plugin's scripts are in `${CLAUDE_SKILL_DIR}/../../scripts/`; `<aip scripts>` is the official aip skill's
`scripts/` folder (see `aip-project`; run those with plain `python3`, they import their sibling modules); the local
workspace copy lives at `gtavi/aip/<clip>/render-engine/`.

Tools this skill calls (Claude Code names): `mcp__plugin_aip_ai-producer__list_workspace`, `mcp__plugin_aip_ai-producer__get_workspace_file`, `mcp__plugin_aip_ai-producer__sign_workspace_upload`, `mcp__plugin_aip_ai-producer__commit_workspace`, `mcp__plugin_aip_ai-producer__wait_task`, `mcp__plugin_aip_ai-producer__get_task`, `mcp__plugin_aip_ai-producer__list_sound_effects`, `mcp__plugin_aip_ai-producer__place_sound_effects`, `mcp__plugin_aip_ai-producer__finish_project`.

## 1. Fetch the workspace

1. `list_workspace` (project_id): keep `digest` for `base_digest`.
2. `get_workspace_file` for `render-engine/index.html` and `render-engine/compositions/editing-script.json`;
   write both into the local `render-engine/` (same relative paths, byte for byte).

## 2. Settle the cut first

Gameplay clips keep the cut as uploaded unless the user asks otherwise. If they want it tighter, edit the
speaker clip pairs in `index.html` under the composition contract before any caption exists: AI Producer refuses
a cut change while caption moments are mounted (`python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_mount.py" <render-engine> --unmount captions-`
takes them out for a recut).

## 3. Time, write, mount

```bash
python3 "<aip scripts>/caption_cues.py" gtavi/aip/m03/render-engine --max-words 3 > gtavi/aip/m03/cues.json
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/captions.py" gtavi/aip/m03/cues.json gtavi/aip/m03/render-engine \
  --look pop --seam-y 668 --emphasis "cops,busted" --hosts-out gtavi/aip/m03/caption-hosts.html
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_mount.py" gtavi/aip/m03/render-engine --list
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_mount.py" gtavi/aip/m03/render-engine --hosts gtavi/aip/m03/caption-hosts.html
```

- `--max-words 3` for `pop`/`karaoke`, `1` for `single`. Correct spelling or casing in cues.json if needed; never
  change what was said. Mild profanity can stay spelled with an asterisk (`f*ck`) when the audio is bleeped.
- `--seam-y` is the `seam_y` that `frame.py` printed for the clip (668 for a 388x240 facecam). Without a seam,
  `--y 0.70` is the official default band.
- Pick the track from `--list` (`next_free_track`) and pass `--track N` if 60 is taken. Captions go last in the
  root, on a track no other moment uses.
- `--alt-cues captions-04:2` colours a cue spoken by someone else (a friend on call, an NPC) in `--alt`.
- Read the JSON warnings: a cue that would wrap to a third line gets split at a word boundary in cues.json.

## 4. Font

The looks use Anton (pop, karaoke, single) or Inter Bold (plain). Stage the file under
`render-engine/public/fonts/` in the same commit: download it from Fontsource (the `source_url_hint` the script
printed) into that folder, or name it in the commit by `source_url` (Fontsource or fonts.gstatic only). A font
that is not staged renders as another face at export.

## 5. Preflight and commit

```bash
python3 "<aip scripts>/preflight.py" gtavi/aip/m03/render-engine --sync-editing-script \
  --remote-file public/source.mp4 --remote-file public/source.mp3 --remote-file public/vendor/gsap.min.js
```

Fix every error. Then `commit_workspace` with `project_id`, `base_digest`, and `expected_files`:
- `render-engine/index.html`, every `render-engine/compositions/captions-NN.html` and
  `render-engine/compositions/editing-script.json` as inline `content` (at most 256 KiB of content per call: split
  long videos into several commits in timeline order, each mounting only the hosts whose files it carries)
- the font by `sha256` after `sign_workspace_upload` + PUT, or by `source_url`
- `authoring: true` on every commit but the last, `false` on the last

`wait_task` on the commit's task, then read `result.accepted`; on a refusal fix each `issues[].repair_hint` and
commit again (re-read `list_workspace` after a 409 or `stale_base`).

## 6. Optional beats, then hand back

- One zoom effect host on the payoff (scale 1.3 or more) and at most one impact shake or white flash per 20 s,
  built as the composition contract's effect and motion hosts. A hook card: see `hooks`.
- Sound effects are on by default in AI Producer: `list_sound_effects`, then one `place_sound_effects` call with
  `replace: true` on the payoff instants. Never `add_sound_effects`.
- `finish_project`, then give the user the `page_url` and ask them to review it there. Export only on an explicit
  request (`aip-export`). Do not open, screenshot or sample the preview or the export yourself.
