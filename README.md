# Rewrite Desk

> A manual document editor with GitHub-style inline diffs — for humanizing AI-generated content section by section.

No install. No build step. No backend. Open `index.html` in any modern browser and start writing.

---

## Quick Start

```
Double-click  index.html  to open in any modern browser.
```

The app works **fully offline** — `vendor/diff.js` and `vendor/marked.min.js` are bundled in the repository. CDN fallback loads automatically if vendor files are absent (e.g. after a raw `git clone` without LFS).

---

## How to Use

1. **Paste, type, or upload** your document on the landing screen  
   (`.txt` or `.md` files accepted via the **Upload** button).
2. The document splits into **sections** on blank lines. Each section becomes an independent editable block with a numbered margin.
3. **Click any section** to open the inline editor.
4. **Type your rewrite** in the draft textarea. The diff preview updates live:
   - <span style="text-decoration:line-through;color:#d99090">red strikethrough</span> = words removed  
   - <span style="text-decoration:underline;color:#8ecf8e">green underline</span> = words added
5. Press **Merge change** (or `Ctrl+Enter`) to commit. Press **Discard** (or `Esc`) to cancel.
6. Merged changes appear in the **History** panel below, newest first. The most recent has an **↩ undo** button.
7. **Export** at any time via `↓ .txt` or `↓ .md` in the toolbar.

---

## Multi-Document Library

The toolbar's document name is a button — click it to open the document switcher:

- **Switch** between saved documents
- **Rename** a document (click ✎)
- **Delete** a document (click ✕)
- **Export JSON** — save the full document state (sections + history + rules) as `docname.state.json`
- **Import JSON** — restore a previously exported state file
- **New document** — starts a blank input view

All documents are stored in `localStorage` under `rewrite-desk-v2`. Migrating from an older single-document session (`rewrite-desk-v1`) happens automatically on first load.

---

## Section Navigation (keyboard)

| Key | Action |
|---|---|
| `J` | Move focus to the next section |
| `K` | Move focus to the previous section |
| `L` | Open the focused section for editing |
| `Ctrl+Enter` / `Cmd+Enter` | Merge the current section |
| `Esc` | Discard the current edit / close any open panel |
| `Tab` | Move through section blocks (standard focus order) |

The toolbar shows a live **section counter** (e.g. `3 / 27`). Click it to scroll to the active or focused section.

> J/K/L activate only when no text field is focused and no panel is open.

---

## Style-Guide Rules

Click **Style rules** in the toolbar to open the rules editor for the current document.

Rules are a JSON array of objects:

```json
[
  { "id": "no-allcaps",  "kind": "banned",   "pattern": "\\b[A-Z]{4,}\\b",          "message": "Avoid ALL CAPS words.",             "enabled": true },
  { "id": "no-emoji",    "kind": "banned",   "pattern": "[\\u{1F300}-\\u{1FFFF}]",   "message": "Avoid emojis.",  "flags": "u",       "enabled": true },
  { "id": "req-we",      "kind": "required", "pattern": "we|our|you",                "message": "Use an inclusive pronoun.",          "enabled": false }
]
```

- `kind: "banned"` — blocks merge if the draft **matches** the pattern  
- `kind: "required"` — blocks merge if the draft **does not match** the pattern  
- `enabled: false` — rule is stored but not enforced  

Three sensible default rules ship with every new document. Rules are stored per-document and persist across reloads. Invalid JSON in the editor shows an inline error; previously saved rules are never lost.

---

## How the Diffing Works

Diffing uses [`diff`](https://www.npmjs.com/package/diff) `v5.2.0` (`diffWordsWithSpace`), running entirely client-side:

1. Both the *current* section text and the *draft* are tokenized into word + whitespace tokens.
2. A [Myers diff](https://neil.fraser.name/writing/diff/myers.pdf) runs on the token arrays.
3. Each token is wrapped in a `<span>` — plain if unchanged, `diff-add` (green underline) if inserted, `diff-remove` (red strikethrough) if deleted.

Insertions are also underlined (in addition to their color) so they are distinguishable without color alone. Deletions use strikethrough.

The diff recomputes on every keystroke, debounced at **150 ms**. The History panel reuses the same function to render before/after diffs for each committed merge.

---

## Where State Is Persisted

Everything lives in **`localStorage`** — no server, no account, no network required.

| Key | Contents |
|---|---|
| `rewrite-desk-v2` | All documents (sections, history, draft, rules), current doc ID |
| `rewrite-desk-v1` | *(Legacy)* Single-document state — auto-migrated on first load |

Autosave is debounced at **400 ms** — the toolbar shows `saving…` then `saved`.

**To wipe all state** (nuclear option):
```javascript
localStorage.removeItem('rewrite-desk-v2');
```

To delete a single document, use the ✕ button in the document switcher.

---

## Edge Cases Handled

| Situation | Behaviour |
|---|---|
| Document with no blank lines | Treated as a single section |
| `\r\n` / `\r` line endings | Normalized to `\n` on import |
| Draft reduced to empty/whitespace | Confirmation prompt before merge |
| Draft identical to current text | Merge blocked with toast |
| Style rule violation | Merge blocked with rule's message shown inline |
| 200+ sections | `content-visibility: auto` skips rendering off-screen sections |
| Offline / no internet | App works fully offline with bundled vendor deps |
| CDN unavailable but vendor present | No fallback needed — vendor files load first |
| Vendor files absent (raw clone) | CDN scripts injected as fallback |

---

## Dependencies

| Dependency | Version | Bundled at |
|---|---|---|
| [`diff`](https://www.npmjs.com/package/diff) | 5.2.0 | `vendor/diff.js` |
| [`marked`](https://www.npmjs.com/package/marked) | 9.1.6 | `vendor/marked.min.js` |

CDN fallbacks: `unpkg.com/diff@5.2.0` and `cdn.jsdelivr.net/npm/marked@9.1.6`.

---

## Development

No tooling required. To contribute:

1. Clone the repo
2. Open `index.html` in your browser — that's the dev server
3. Edit and reload

Vendor files are committed in `vendor/`. To re-verify or update them:

```powershell
Invoke-WebRequest -Uri "https://unpkg.com/diff@5.2.0/dist/diff.js"          -OutFile "vendor/diff.js"
Invoke-WebRequest -Uri "https://cdn.jsdelivr.net/npm/marked@9.1.6/marked.min.js" -OutFile "vendor/marked.min.js"
```

See [CONTRIBUTING.md](CONTRIBUTING.md) for branch naming, commit style, and the manual test checklist.

---

## Roadmap

**Shipped in v0.1.0**
- ✅ Multi-document library (create, switch, rename, delete, import/export JSON)
- ✅ Section keyboard navigation (J / K / L, section counter)
- ✅ Local style-guide rules (banned / required patterns, per-document, default rules)
- ✅ Offline mode (vendored dependencies, CDN fallback)
- ✅ Merge history with undo
- ✅ Markdown rendering in read-only view
- ✅ WCAG AA contrast, `prefers-reduced-motion`, full keyboard navigation

**Deferred to future tiers**
- Team review of merges (requires a backend: `POST /merges`, approval dashboard)
- Cloud sync / cross-device history (IndexedDB → remote store)
- `.docx` / `.pdf` export (requires a server-side conversion service)
- AI rewriting suggestions — explicitly out of scope for this tool; the human types the replacement

See [CHANGELOG.md](CHANGELOG.md) for the full feature list and [releases/README.md](releases/README.md) for the release process.

---

## For Contributors

Read [CONTRIBUTING.md](CONTRIBUTING.md) before opening a PR. Please also review the [Code of Conduct](CODE_OF_CONDUCT.md) and [Security Policy](SECURITY.md).

---

## Browser Support

Works in any modern browser: **Chrome 85+, Edge 88+, Firefox 125+, Safari 16.4+**.  
`content-visibility: auto` is a progressive enhancement — gracefully ignored in older browsers.

---

## License

[MIT](LICENSE) — © 2026 Rewrite Desk Contributors
