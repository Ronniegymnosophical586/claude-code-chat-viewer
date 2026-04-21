# Changelog

All notable changes to this project are documented here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

Nothing yet.

## [0.1.0] — 2026-04-22

First public release.

### Added

- Single-file HTML viewer for Claude Code JSONL transcripts.
- Six UI languages with live switcher: English, Русский, Español, Français, 中文, العربية. Arabic auto-switches to RTL.
- Light / dark theme with automatic detection from the OS `prefers-color-scheme`. Manual click pins the choice.
- Drag-and-drop file loading — drop anywhere on the page. Folders and non-file drags are rejected cleanly.
- Reader mode — one toggle that hides all service entries, leaving only real user messages and assistant text.
- Copy-per-message button in every entry header. Clipboard API with `document.execCommand` fallback for `file://` contexts. ✓ confirmation for 1.2 s after copy.
- Friendly tool names — 16 known Claude Code tools get emoji + label (`📖 Read file`, `🖥️ Shell`, `📝 Edit file`, …). Unknown / MCP tools keep `🔧 {raw_name}`.
- Bundled `demo.jsonl` + "Try with example" button in the empty state (online only — `fetch()` is blocked on `file://`).
- Five category filters: thinking, tools, results, system/meta, ui-state. Toggle via a single CSS class, no DOM reflow.
- Streaming JSONL parse via `file.stream()` + `TextDecoderStream` — no full-string load.
- Native virtualization via `content-visibility: auto` on each entry.
- Chunked rendering — 500 records per chunk, "Load more" button for the rest.
- Size caps — 20 KB prose / 5 KB service blocks. Stringification is also bounded.
- `.json` fallback — plain JSON arrays/objects are parsed as records too.

### Security / privacy

- XSS-safe markdown rendering: every text block is HTML-escaped before `marked.parse` so raw HTML can never reach the DOM.
- Markdown images are neutralized — rendered as inert text with visible URL, never fetched. Prevents a crafted transcript from leaking the viewer's IP / usage via tracker images.
- External links restricted to `http(s)` and opened with `rel="noopener noreferrer nofollow" target="_blank"`. Other schemes (`javascript:`, `data:`, `file:`, `mailto:`) are rendered as plain text.
- `localStorage` access wrapped in try/catch — survives strict privacy modes.

### Packaging

- Unlicense (public domain, no attribution required).
- GitHub Pages hosted live demo.
- Single-file release build via CI — one HTML with marked.min.js inlined, available as a release asset.

[Unreleased]: https://github.com/hitmman55/claude-code-chat-viewer/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/hitmman55/claude-code-chat-viewer/releases/tag/v0.1.0
