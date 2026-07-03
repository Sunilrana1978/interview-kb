# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A static web app — no build system, no package manager, no test runner. It is a collection of self-contained HTML study guides published via GitHub Pages at `https://sunilrana1978.github.io/interview-kb/`.

## Viewing Locally

```bash
open index.html                        # main landing page
open "kubernetes-guide.html"           # any individual guide
```

No server required. Every file is a standalone HTML page.

## Deploying

```bash
git add <files>
git commit -m "your message"
git push origin master                 # GitHub Pages auto-deploys from master
```

## Architecture

### `index.html` — the app shell

Single-file app with all CSS and JS inline. Key sections:

- **CSS tokens** — `:root` (light) and `html.dark` (dark mode) variable blocks control the entire color system
- **Layout** — CSS Grid: fixed blue top nav (`54px`) + sticky sidebar (`220px`) + scrollable main content
- **Theme** — an inline `<script>` at the top of `<head>` reads `localStorage` and applies `html.dark` before paint to avoid flash. Theme toggle writes back to `localStorage`.
- **Sidebar filter** — clicking a `.sidebar-item[data-filter]` toggles `all-hidden` on sections whose `data-cat` doesn't match
- **Search** — live `input` listener iterates `.card` elements, toggling `.hidden`; press `/` to focus, `Escape` to blur

### Guide pages (`*.html`)

Each guide is fully self-contained with its own embedded CSS, fonts, and content. They share no stylesheet with `index.html`. Every guide has two snippets injected at the top:

1. **Theme sync** — inline `<script>` in `<head>` that reads `localStorage` and applies `html.light` (guide pages invert via CSS filter, opposite convention to `index.html`)
2. **Floating nav** — injected after `<body>`: a fixed Home button + theme toggle button (top-left, `z-index: 9999`)

Guide pages use **CSS filter inversion** (`invert(1) hue-rotate(180deg)`) for light mode, since each page has its own unique dark-theme CSS that can't be trivially overridden.

### Theme convention difference

| File | Dark class | Light mode mechanism |
|---|---|---|
| `index.html` | `html.dark` | CSS custom properties |
| Guide pages | `html.light` | `filter: invert(1) hue-rotate(180deg)` |

### Bulk-modifying guide pages

Use Python to inject into all 41 guides at once — the pattern established in this project:

```python
import os, re
directory = "/Users/sunilkumar/Documents/Personal/Interview Questions"
files = [f for f in os.listdir(directory)
         if f.endswith('.html') and f != 'index.html' and not f.startswith('~')]
for fname in files:
    path = os.path.join(directory, fname)
    with open(path, 'r', encoding='utf-8', errors='ignore') as f:
        content = f.read()
    # modify content ...
    content = re.sub(r'(</head>)', YOUR_SNIPPET + r'\n\1', content, count=1, flags=re.IGNORECASE)
    with open(path, 'w', encoding='utf-8') as f:
        f.write(content)
```

### Adding a new guide card to `index.html`

Find the correct `<div class="section cat-X">` block and add a card inside `.grid`:

```html
<a class="card" href="your-file.html">
  <div class="card-top">
    <div class="card-title">Guide Title</div>
    <div class="card-arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="7" y1="17" x2="17" y2="7"/><polyline points="7 7 17 7 17 17"/></svg></div>
  </div>
  <div class="card-desc">One-line description of the guide</div>
  <div class="card-footer"><span class="card-tag">Tag</span></div>
</a>
```

Also update the sidebar badge count for that category and run the Python injection script on the new guide file.

## Git

- Branch: `master` (not `main`)
- Remote: `https://github.com/Sunilrana1978/interview-kb.git`
- `.gitignore` excludes: `*.docx`, `*.pdf`, `*.zip`, `*.sql`, images, and Word temp files (`~$*`)
