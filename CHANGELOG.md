# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-10-01

### Added

- Single-file HTML/JS/CSS application, no build step required
- Three input methods: paste, type, upload `.txt`/`.md`
- Section splitting on blank lines with section number gutter
- Click-to-edit inline section editing (never the whole document)
- Word-level diff preview via diff@5.2.0 (`diffWordsWithSpace`), debounced 150 ms
- Merge/Discard actions with `Ctrl+Enter` / `Esc` keyboard shortcuts
- Merge history panel with timestamp and full diff per entry
- Undo last merge from history
- Edited section markers (amber left border + gutter pip)
- Export as `.txt` or `.md`
- Autosave to `localStorage` (400 ms debounce)
- Session restore on page reload
- Multi-document library: create, switch, rename, delete, export/import documents as JSON
- Section keyboard navigation: `J`/`K` (next/prev), `L` (open editor), section counter in toolbar
- Local style-guide rules: per-document regex rules (banned/required) that block merges; default rules included
- Vendored dependencies (`vendor/diff.js`, `vendor/marked.min.js`) for offline use; CDN fallback if absent
- Markdown rendering in read-only view (marked@9.1.6)
- Dark editorial aesthetic: warm near-black palette, Source Serif 4 body, monospace UI chrome
- WCAG AA contrast, `prefers-reduced-motion` support, keyboard-navigable throughout

[Unreleased]: https://github.com/rewritedesk/rewritedesk/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/rewritedesk/rewritedesk/releases/tag/v0.1.0
