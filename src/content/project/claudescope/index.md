---
title: "claudescope"
description: "A desktop app that shows the bash commands Claude Code has run in a project — as a live tail while it works, or browsable history from a past session."
publishDate: "2026-07-22"
tags: ["tauri", "rust", "claude-code", "desktop"]
coverImage:
  src: "./cover.png"
  alt: "claudescope desktop app showing a history view of Claude Code bash commands, including git status, git log, ls, and find, each with their output"
repoUrl: "https://github.com/Kushalgouda-Patil/claudescope"
pinned: false
---

Claude Code logs every session as a JSONL file under `~/.claude/projects/<encoded-project-path>/<session-id>.jsonl`. claudescope reads those files, pairs up each `Bash` tool call with its result, and shows you:

- **Live** — pick a project, hit "Start watching," and see bash commands appear as Claude Code runs them, in real time.
- **History** — pick any past session for a project and browse every bash command it ran, with output and pass/fail status.

Failed commands (`is_error: true`) are highlighted in red. Each command and its output has a copy button.

Built with [Tauri v2](https://v2.tauri.app/) — a Rust backend, a plain HTML/CSS/JS frontend, no bundler, no Node.js required to build or run it. The Rust backend exposes five commands to the frontend: `list_projects`, `list_sessions`, `read_session_bash_commands`, `start_watching`, and `stop_watching`. JSONL parsing pairs each `tool_use` (a Bash call) with its later `tool_result` by id. The live feed uses the [`notify`](https://docs.rs/notify) crate to watch the active session file; on each write it reads only the newly appended bytes and emits any newly-completed commands to the frontend over Tauri's event system.
