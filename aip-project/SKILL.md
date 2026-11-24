---
name: aip-project
description: Open finished 1080x1920 GTA 6 clips as AI Producer projects (the harness) - check pricing, sign the upload, PUT the bytes, start transcription, wait, and hand the user the editable project link. Use after cut-and-frame or vodcut prep, or when the user says "open this in AI Producer" for a local clip.
argument-hint: "[clip path(s)]"
---

# Open a clip in AI Producer

AI Producer by OpusClip is the harness: it hosts the project, the word-level transcript, the caption
workspace, the timeline, versions and the export, and every project gets a durable `page_url` the user can
open and edit. This skill takes a clip from your disk to that link (the "cut locally, then upload" path).

It depends on the official plugin `aip@ai-producer-plugins` and its tools, named
`mcp__plugin_aip_ai-producer__<tool>` in Claude Code. Read the official `aip:aip` skill once per session: its
settings, pricing, talk-to-the-user and delivery rules apply here too; this skill only adds the GTA specifics.

Tools this skill calls (Claude Code names): `mcp__plugin_aip_ai-producer__get_pricing`, `mcp__plugin_aip_ai-producer__get_editing_preset`, `mcp__plugin_aip_ai-producer__sign_upload`, `mcp__plugin_aip_ai-producer__start_transcribe_project`, `mcp__plugin_aip_ai-producer__wait_task`, `mcp__plugin_aip_ai-producer__get_transcript`, `mcp__plugin_aip_ai-producer__finish_project`, `mcp__plugin_aip_ai-producer__list_reference_presets`, `mcp__plugin_aip_ai-producer__get_reference_preset`.

## 0. Before anything

- Tools missing, or an OAuth prompt: the user signs in with `/mcp` -> `plugin:aip:ai-producer`, then starts a
  new session. Follow `aip:aip-setup` if they have never connected. Never ask for or paste a password, cookie
  or token, never edit MCP config, never reinstall for them.
- Every AI Producer call carries `context` (15-25 words, third person, no names or personal details, e.g.
  "Starting a project from an uploaded vertical gameplay clip so the requested captions can be added.") and
  `llm_model` (your model id, or "unknown").
- Call it "AI Producer" to the user. Never show tool names, ids, digests or signed URLs.

## 1. Price and defaults (once per session / per project)

1. `get_pricing` with `{}` (no project yet). It is also the sign-in check. If preparation is in the `free`
   band, say nothing about money; if a row can charge, tell the user the price in plain words before starting,
   as the official skill says. Never quote prices from memory.
2. `get_editing_preset` fresh for every new project. A saved preset fills settings the user did not state.

## 2. Upload

```bash
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_prep.py" check gtavi/runs/<slug>/clips/m03_9x16.mp4
python3 -I "${CLAUDE_SKILL_DIR}/../../scripts/aip_prep.py" sign-args gtavi/runs/<slug>/clips/m03_9x16.mp4
```

`check` must pass: .mp4/.mov/.webm, at most 3584 MiB, at most 30 min for most accounts (the web guide says 15),
portrait. A landscape file fails it (AI Producer would keep it 16:9): go back to `cut-and-frame`. Only a source
that is meant to stay 16:9, such as a landscape talking head, gets `check --allow-landscape`. Then:

1. `sign_upload` with exactly the `filename`, `content_type`, `size`, `sha256` that `sign-args` printed.
2. PUT the bytes with every returned header. Use the official helper (it never prints the URL): write
   `[{"path": "m03_9x16.mp4", "upload_url": "<upload_url>", "headers": {<headers>}}]` to a temporary file
   outside the project, pipe it to `python3 "<aip scripts>/upload_batch.py" gtavi/runs/<slug>/clips < tmp.json`,
   then delete the file. `<aip scripts>` is the official aip skill's `scripts/` folder (its base directory when
   loaded; or `ls -d ~/.claude/plugins/cache/ai-producer-plugins/aip/*/skills/aip/scripts`). Run official helpers
   with plain `python3`: they import their sibling modules. A failed PUT may be repeated until the target expires.
3. `start_transcribe_project` with `video_ref`, `sha256`, `filename`, `aspect_ratio: "9:16"`, `authoring: true`
   and a `prompt` in the user's words, for example: "Vertical GTA 6 stream clip of IShowSpeed, facecam on top and
   gameplay below. Keep the cut as uploaded, add word-by-word captions in a bold gaming style, credit IShowSpeed."
   Watch the reply for `aspect_ratio_adjusted` (the file was not portrait: go back to `cut-and-frame`) and
   `speechless_source` (no speech: no captions).
4. Give the user the `page_url` in the same reply that says the project has started.

## 3. The reference question

A new project asks one question about a reference style (preset, link, video, or skip), exactly as the official
skill words it. For a batch of clips, ask it once for the whole batch and apply the answer to every project. The
GTA caption look of this plugin (`aip-captions`) is a fine answer to "skip". For GTA clips, offer the Gaming
presets first (`list_reference_presets`, category `gaming`): Stream Split Third or Half Split Reactions for a
cam-over-gameplay single, Layout Switch Getaway for a story cut, Game UI Countdown for a countdown. They copy the
look only: AI Producer still doesn't reframe, so the 9:16 frame is composed locally first (`cut-and-frame`).

## 4. Wait and record

`wait_task` on the returned `task_id` (`wait_seconds` 270, repeat while not terminal; at most 8 waits at once).
Then `get_transcript` with `detail: "segments"` to confirm the words landed. Write `project_id` and `page_url` into
the clip's `.clip.json` (`aip` block). For several clips, start them all before waiting on any.

`authoring: true` marks the project as still being edited (it lapses 30 minutes after the last call that set it);
`finish_project` is what ends that status and unlocks the editor. If no caption or edit pass follows in this
session (the user stops at the project link, the default delivery), call `finish_project` once `wait_task`
succeeds, then give the `page_url`, so the link opens an editor the user can change. When `aip-captions` or
another edit follows, that skill calls `finish_project` at its end instead.

## 5. Next

- Captions in the GTA look: `aip-captions`.
- Anything else the user asks for (motion graphics, music, sound effects, a reference style): follow the
  official `aip:aip` skill on this project.
- An MP4 only when the user asks for one: `aip-export`.

Rough cut: keep it `off` for gameplay clips unless the user asks for a tighter cut. The window was already
chosen in `pick-moments`, and removing "filler" from screams and laughs breaks the reaction.
