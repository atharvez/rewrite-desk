# Release Process

This document describes how to cut a new release of Rewrite Desk. There is no CI/CD pipeline — releases are created manually by a maintainer.

---

## Tag Format

Rewrite Desk uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html). Git tags are prefixed with `v`:

```
v0.1.0
v0.2.0
v1.0.0
```

---

## Versioning Policy

| Change type | Version bump | Example |
|---|---|---|
| Bug fixes, typos, documentation corrections | **Patch** (`0.0.x`) | `v0.1.0` → `v0.1.1` |
| New features that are backwards-compatible | **Minor** (`0.x.0`) | `v0.1.0` → `v0.2.0` |
| Breaking changes to the `localStorage` state schema | **Major** (`x.0.0`) | `v0.1.0` → `v1.0.0` |

> **Note on major versions:** A major version bump is required whenever the shape of data stored in `localStorage` changes in a way that is not backwards-compatible — i.e., an existing user's saved documents or settings would be lost, corrupted, or silently migrated on upgrade. Include a migration notice in the GitHub Release description whenever this occurs.

---

## How to Cut a Release

Follow these steps in order. All steps are performed locally unless noted.

### Step 1 — Update `CHANGELOG.md`

Move all items from the `[Unreleased]` section into a new dated release section:

```markdown
## [0.2.0] - YYYY-MM-DD

### Added
- ...

### Fixed
- ...
```

Update the comparison links at the bottom of the file:

```markdown
[Unreleased]: https://github.com/rewritedesk/rewritedesk/compare/v0.2.0...HEAD
[0.2.0]: https://github.com/rewritedesk/rewritedesk/compare/v0.1.0...v0.2.0
```

Commit this change:

```bash
git add CHANGELOG.md
git commit -m "chore(release): update changelog for v0.2.0"
```

### Step 2 — Verify Vendor Dependencies

Confirm that the vendored files in `vendor/` are at the correct versions for this release:

| File | Expected package | Expected version |
|---|---|---|
| `vendor/diff.js` | [diff](https://github.com/kpdecker/jsdiff) | 5.2.0 |
| `vendor/marked.min.js` | [marked](https://marked.js.org) | 9.1.6 |

To verify a file's version, check the comment header at the top of the file (e.g., `/*! diff v5.2.0 */`) or cross-reference the file hash against the official release on [unpkg.com](https://unpkg.com).

If any vendor file needs updating:

1. Download the new UMD/browser build.
2. Replace the file in `vendor/`.
3. Update version references in `index.html` and this document.
4. Test offline mode (disable network, hard-reload, verify all diff and markdown features work).
5. Commit the update: `chore(vendor): update diff to X.Y.Z`

### Step 3 — Create the Git Tag

Tag the release on the commit that includes the updated changelog:

```bash
git tag v0.2.0
```

To annotate the tag with a short message (recommended):

```bash
git tag -a v0.2.0 -m "Release v0.2.0"
```

### Step 4 — Push the Tag

Push both the commit and the tag to the remote:

```bash
git push origin main
git push origin v0.2.0
```

### Step 5 — Create the GitHub Release

1. Go to the repository on GitHub → **Releases** → **Draft a new release**.
2. Select the tag `v0.2.0` you just pushed.
3. Set the release title to `v0.2.0`.
4. Paste the relevant section from `CHANGELOG.md` into the release description.
5. Assemble the release ZIP archive. It must contain exactly:

   ```
   rewritedesk-v0.2.0/
   ├── index.html
   ├── vendor/
   │   ├── diff.js
   │   └── marked.min.js
   ├── LICENSE
   └── README.md
   ```

   Create the archive:

   ```bash
   # From the repo root
   zip -r rewritedesk-v0.2.0.zip index.html vendor/ LICENSE README.md
   ```

   On Windows (PowerShell):

   ```powershell
   Compress-Archive -Path index.html, vendor, LICENSE, README.md `
     -DestinationPath rewritedesk-v0.2.0.zip
   ```

6. Attach `rewritedesk-v0.2.0.zip` to the GitHub Release.
7. Publish the release.

---

## No CI/CD Required

At this stage of the project there is no automated build, test, or release pipeline. All release steps are performed manually by a maintainer. If a CI/CD pipeline is introduced in the future, this document should be updated accordingly.

---

## Release Checklist Summary

- [ ] `CHANGELOG.md` updated and committed
- [ ] Vendor dependency versions verified
- [ ] All items on the [Testing Checklist](../CONTRIBUTING.md#testing-checklist) pass
- [ ] Git tag created (`git tag -a vX.Y.Z -m "Release vX.Y.Z"`)
- [ ] Tag pushed (`git push origin vX.Y.Z`)
- [ ] GitHub Release created with changelog notes
- [ ] ZIP archive attached (`index.html` + `vendor/` + `LICENSE` + `README.md`)
- [ ] Release published
