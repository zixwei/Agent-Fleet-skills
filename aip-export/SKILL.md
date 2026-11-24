---
name: aip-export
description: Export a finished AI Producer GTA 6 project to a 1080p (max 30 fps) MP4 and save it locally, then deliver the editable project link and the file path. Use only when the user explicitly asks for the MP4, a rendered file, or an export; otherwise the project link is the delivery.
argument-hint: "[project or clip]"
---

# Export and deliver

The default delivery is the editable project link (`page_url`). An MP4 is rendered only on an explicit request
("export it", "give me the mp4", "I want to post it"), as the official `aip:aip` skill says. Every call carries
`context` and `llm_model`, as in `aip-project`.

Tools this skill calls (Claude Code names): `mcp__plugin_aip_ai-producer__finish_project`, `mcp__plugin_aip_ai-producer__start_export`, `mcp__plugin_aip_ai-producer__wait_task`, `mcp__plugin_aip_ai-producer__get_export_url`.

## 1. Make sure the edit is handed back

- The last `commit_workspace` was accepted (`wait_task` result) and closed authoring (`authoring: false`).
- `finish_project` has run on the project (it unlocks the editor; required before an export since plugin 1.2.15).
  It refuses a busy or unsettled project: wait for the running task first.

## 2. Render

1. `start_export` (project_id). It checks the preview renders first; a refusal lists the blocking issues
   (fix them in the workspace and commit again). `deferred` means a live turn owns the project and the export
   starts when it ends. A fresh export of the same preview is reused.
2. `wait_task` on the returned `task_id` until it is done.
3. `get_export_url` (project_id). `ready` carries `download_url` and `dropped_visuals` (moments that failed to
   render: tell the user which, in plain words).

## 3. Download at once

`download_url` is a one-use credential for this account: never paste it into a reply, a file or a log, and
never hand it to anyone. Download it right away:

```bash
mkdir -p gtavi/exports && curl -sS -L --fail -o gtavi/exports/m03.mp4 "<download_url>"
```

If it expired, call `get_export_url` again.

## 4. Deliver

- The `page_url` (durable, editable) and the local file path. Nothing else.
- What the file is: one MP4, up to 1080p, at most 30 fps, in the source orientation (9:16 here).
- Do not open, play, screenshot, sample or analyse the export: the official skill stops at delivery, and so does
  this one. The user reviews it.
- Next: `post-copy` for the captions and credits per platform, and `scoreboard add` once it is posted.

Never claim the clip was "made by AI Producer" unless it was edited there; in this workflow the cut and the
framing are made locally and AI Producer hosts the edit, captions and export.
