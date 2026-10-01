# Contributing to Rewrite Desk

Thank you for your interest in contributing! Rewrite Desk is intentionally simple — a single HTML file you can open in a browser. This guide covers everything you need to get started.

---

## Table of Contents

1. [Running the App](#running-the-app)
2. [Vendor Dependencies & Offline Mode](#vendor-dependencies--offline-mode)
3. [Reporting Bugs](#reporting-bugs)
4. [Branch Naming](#branch-naming)
5. [Commit Style](#commit-style)
6. [Pull Request Guidelines](#pull-request-guidelines)
7. [Code Style](#code-style)
8. [Testing Checklist](#testing-checklist)

---

## Running the App

No installation, no build step, no Node.js required.

1. Clone or download the repository.
2. Open `index.html` in any modern browser (Chrome 110+, Firefox 115+, Safari 16+, Edge 110+).

That's it. The app runs entirely in your browser.

```
# Example (macOS / Linux)
open index.html

# Example (Windows)
start index.html
```

If you prefer a local HTTP server (useful for testing `fetch()` behaviour with vendor files), any static file server works:

```bash
# Python 3
python -m http.server 8080
# then open http://localhost:8080
```

---

## Vendor Dependencies & Offline Mode

Rewrite Desk ships with its runtime dependencies vendored locally so the app works without an internet connection:

| File | Package | Version |
|---|---|---|
| `vendor/diff.js` | [diff](https://github.com/kpdecker/jsdiff) | 5.2.0 |
| `vendor/marked.min.js` | [marked](https://marked.js.org) | 9.1.6 |

The app loads vendor files first. If a vendor file is absent, it falls back to the CDN version. **For offline or air-gapped use, always ensure the `vendor/` directory is present.**

When updating a vendored dependency:

1. Download the new UMD/browser build from the package's GitHub releases or `unpkg.com`.
2. Replace the file in `vendor/`.
3. Update the version reference in `index.html` (comment near the `<script>` tag) and in `releases/README.md`.
4. Verify the app still works offline by disabling your network connection and reloading.

---

## Reporting Bugs

Please open an issue on [GitHub Issues](https://github.com/rewritedesk/rewritedesk/issues/new?template=bug_report.md).

A good bug report includes:

- **Browser and version** — e.g., Chrome 126.0.6478.126, Firefox 128.0
- **Operating system** — e.g., Windows 11, macOS 14, Ubuntu 24.04
- **Steps to reproduce** — numbered, minimal steps that reliably trigger the bug
- **Expected behaviour** — what you expected to happen
- **Actual behaviour** — what actually happened, including any console errors
- **localStorage snapshot** *(if relevant)* — open DevTools → Application → Local Storage, copy the relevant keys and paste them into the issue. **Redact any personal text before sharing.**

To copy your localStorage snapshot quickly, paste this into the browser console:

```js
JSON.stringify(
  Object.fromEntries(
    Object.keys(localStorage)
      .filter(k => k.startsWith('rwd_'))
      .map(k => [k, localStorage.getItem(k)])
  ),
  null,
  2
)
```

---

## Branch Naming

Use one of the following prefixes, followed by a short kebab-case description:

| Prefix | Use for |
|---|---|
| `feat/` | New features |
| `fix/` | Bug fixes |
| `docs/` | Documentation changes only |
| `chore/` | Maintenance, dependency updates, tooling |

**Examples:**

```
feat/section-keyboard-nav
fix/localstorage-restore-blank-doc
docs/contributing-vendor-guide
chore/update-diff-5.2.0
```

Branch names should be lowercase and use hyphens, not underscores or spaces.

---

## Commit Style

Rewrite Desk uses [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

**Format:**

```
<type>(<optional scope>): <short description>

[optional body]

[optional footer(s)]
```

**Types:**

| Type | When to use |
|---|---|
| `feat` | A new feature visible to users |
| `fix` | A bug fix |
| `docs` | Documentation only (no code change) |
| `chore` | Build process, vendor updates, config — no production code change |

**Examples:**

```
feat(nav): add J/K keyboard navigation between sections
fix(diff): debounce not resetting on rapid keystrokes
docs(contributing): add localStorage snapshot instructions
chore(vendor): update diff to 5.2.0
```

Keep the subject line under 72 characters. Use the body to explain *why*, not *what*.

---

## Pull Request Guidelines

- **Keep PRs small and focused.** One feature or fix per PR. If you find an unrelated bug while working, open a separate issue or PR.
- **Update `CHANGELOG.md`.** Add your change under the `[Unreleased]` section using [Keep a Changelog](https://keepachangelog.com) format.
- **Do not bundle unrelated changes.** Refactoring, formatting fixes, and feature work should be in separate commits at minimum, ideally separate PRs.
- **Describe your change clearly** in the PR description: what changed, why, and how to test it manually.
- **Link the relevant issue** if one exists (`Closes #123`).
- **All items on the [Testing Checklist](#testing-checklist) must pass** before requesting review.

PRs that do not include a `CHANGELOG.md` update, break offline mode, or reduce accessibility will be asked to revise before merging.

---

## Code Style

Rewrite Desk has a deliberate no-tooling philosophy. There is no linter, no formatter, no transpiler, and no bundler. Please respect this:

- **Plain JavaScript only.** No TypeScript, no JSX, no compile step.
- **No frameworks.** No React, Vue, Svelte, or similar. DOM manipulation is done directly.
- **Single file.** All HTML, CSS, and JS lives in `index.html`. Do not split into separate files or introduce a build pipeline.
- **No `npm install` for users.** Runtime dependencies are vendored in `vendor/`. Dev tooling should not be required to run or contribute to the app.
- **ES2020+ is fine.** Target browsers support optional chaining, nullish coalescing, `Array.at()`, `structuredClone()`, etc. No need to polyfill these.
- **CSS custom properties** for all colours and spacing. Follow the existing `--rwd-*` naming convention.
- **Accessibility first.** All interactive elements must be keyboard-accessible and have appropriate ARIA labels. Maintain WCAG AA contrast ratios. Respect `prefers-reduced-motion`.
- **Comment non-obvious logic.** A brief `//` comment above any regex, debounce, or diff-related code is appreciated.
- **Consistent indentation.** Two spaces. No tabs.

---

## Testing Checklist

Rewrite Desk has no automated test suite — all testing is manual. Before submitting a PR, verify every item below passes in at least one Chromium-based browser and one Firefox-based browser.

### Core Functionality

- [ ] **Hard reload** — Press `Ctrl+Shift+R` (or `Cmd+Shift+R`). The page reloads cleanly with no JS errors in the console.
- [ ] **Session restore** — Type or paste content, wait 400 ms for autosave, then close and reopen the tab. All documents, sections, merge history, and style-guide rules are restored exactly as left.
- [ ] **Section split** — Paste multi-paragraph text. Confirm sections split on blank lines and section numbers appear in the gutter.
- [ ] **Inline editing** — Click a section, make a change, confirm diff preview appears within 150 ms of stopping typing.
- [ ] **Merge** — Press `Ctrl+Enter`. Confirm the section updates, a history entry is appended with a timestamp, and the amber edited marker appears.
- [ ] **Discard** — Press `Esc` while editing. Confirm the editor closes and the section reverts to its last saved state.
- [ ] **Undo merge** — Open merge history, click Undo on the latest entry. Confirm the section reverts and the history entry is removed.
- [ ] **Export** — Export as `.txt` and as `.md`. Open the downloaded files and verify content is correct.
- [ ] **Multi-document library** — Create a second document, switch between documents, rename and delete a document.
- [ ] **Import/Export JSON** — Export a document as JSON, delete it, re-import the JSON, and confirm all content is restored.
- [ ] **Style-guide rules** — Add a banned-word rule. Type the banned word in an edited section and attempt to merge. Confirm the merge is blocked and an error is shown.

### Offline Mode

- [ ] **Offline mode** — Disable your network connection (or use DevTools → Network → Offline). Reload the page. Confirm the app loads and all features work using the vendored `vendor/diff.js` and `vendor/marked.min.js`.

### Keyboard Navigation

- [ ] **J / K navigation** — With no editor open, press `J` and `K` to move between sections. Confirm the focused section is visually highlighted.
- [ ] **L to edit** — Press `L` on a focused section. Confirm the inline editor opens.
- [ ] **Section counter** — Confirm the toolbar section counter (e.g., `3 / 12`) updates as you navigate.

### Accessibility & Motion

- [ ] **prefers-reduced-motion** — In OS settings, enable "Reduce Motion" (or simulate via DevTools → Rendering → Emulate CSS media feature). Reload and confirm all transitions and animations are suppressed.
- [ ] **Keyboard-only navigation** — Tab through the entire UI without using a mouse. Confirm every button, input, and interactive element is reachable and has a visible focus ring.
- [ ] **Contrast check** — Use the browser's accessibility inspector or a tool like [axe DevTools](https://www.deque.com/axe/) to verify no WCAG AA contrast failures are introduced by your change.
