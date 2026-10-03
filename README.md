# Rewrite Desk

A single-file document editor with inline diffs for humanizing AI-generated content. No build step, no dependencies.

## Overview

AI-generated content often needs a human touch. Rewrite Desk provides a distraction-free environment to edit text with live word-level diffs -- perfect for refining AI output into polished prose.

## Features

- Inline diffs -- word-level change highlighting
- Zero dependencies -- single HTML file, runs anywhere
- Before/after view -- toggle between original and rewritten
- Auto-save -- changes persist in localStorage
- Clean UI -- distraction-free writing environment

## Quick Start

```bash
# No installation needed
open index.html

# Or serve locally:
python -m http.server 8080
```

## How It Works

1. Paste AI-generated text into the Original pane
2. Edit freely in the Rewrite pane
3. See changes highlighted in real-time
4. Copy the final polished text

## License

MIT (c) Atharva Desai