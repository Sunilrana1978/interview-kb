# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repository Is

A static web app — no build system, no package manager, no test runner. It is a collection of self-contained HTML study guides published via GitHub Pages at `https://sunilrana1978.github.io/interview-kb/`.

## Folder Structure

```
/
├── index.html          # app shell (must stay at root for GitHub Pages)
├── favicon.svg
├── README.md
├── CLAUDE.md
├── .gitignore
└── guides/
    ├── ai-rag/          # AI & RAG
    ├── cloud-aws/       # Cloud & AWS
    ├── devops/          # DevOps & Infrastructure
    ├── system-design/   # System Design
    ├── data-engineering/
    ├── security/
    ├── programming/
    └── interview-prep/
        └── *.html       # self-contained guide pages, one topic folder per sidebar category
```

Guide pages live two levels deep (`guides/<topic>/file.html`), so their injected nav uses `../../index.html` and `../../favicon.svg` (not `../`).

## Viewing Locally

```bash
open index.html                                        # main landing page
open "guides/devops/kubernetes-guide.html"              # any individual guide
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

### Guide pages (`guides/<topic>/*.html`)

Each guide is fully self-contained with its own embedded CSS, fonts, and content, and lives under a topic subfolder (`ai-rag`, `cloud-aws`, `devops`, `system-design`, `data-engineering`, `security`, `programming`, `interview-prep`). Every guide has these snippets injected:

1. **Anti-flash script** — inline `<script>` in `<head>` reads `localStorage` and applies `html.dark` before paint
2. **Shared CSS** (`<style id="kb-shared">`) — injected before `</head>`: overrides all guide pages with Inter font, consistent light/dark colours, table and code block styles
3. **Blue topnav** — injected after `<body>`: fixed full-width nav with "Interview KB" brand, `← All Guides` link back to `../../index.html`, and theme toggle

Because guides sit two levels deep, both the favicon `<link>` and the topnav links use `../../` — not `../`.

### Theme convention

Both `index.html` and guide pages use the same convention:

| State | Class | Mechanism |
|---|---|---|
| Light (default) | *(no class)* | base CSS |
| Dark | `html.dark` | CSS variable / `!important` overrides |

`localStorage` key `theme` stores `'dark'` or `'light'`.

### Bulk-modifying guide pages

Use Python to modify all guides at once (walk topic subfolders recursively):

```python
import os, re
root = "/Users/sunilkumar/Documents/Personal/Interview Questions/guides"
for dirpath, _, filenames in os.walk(root):
    for fname in filenames:
        if not fname.endswith('.html') or fname.startswith('~'):
            continue
        path = os.path.join(dirpath, fname)
        with open(path, 'r', encoding='utf-8', errors='ignore') as f:
            content = f.read()
        # modify content ...
        content = re.sub(r'(</head>)', YOUR_SNIPPET + r'\n\1', content, count=1, flags=re.IGNORECASE)
        with open(path, 'w', encoding='utf-8') as f:
            f.write(content)
```

### Adding a new guide

1. Place the HTML file in the matching `guides/<topic>/` folder (`ai-rag`, `cloud-aws`, `devops`, `system-design`, `data-engineering`, `security`, `programming`, `interview-prep`)
2. Ensure it has the standard nav injected, using `../../index.html` and `../../favicon.svg` (two levels deep)
3. Add a card to the correct `<div class="section cat-X">` block in `index.html`:

```html
<a class="card" href="guides/<topic>/your-file.html">
  <div class="card-top">
    <div class="card-title">Guide Title</div>
    <div class="card-arrow"><svg viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"><line x1="7" y1="17" x2="17" y2="7"/><polyline points="7 7 17 7 17 17"/></svg></div>
  </div>
  <div class="card-desc">One-line description of the guide</div>
  <div class="card-footer"><span class="card-tag">Tag</span></div>
</a>
```

4. Update the sidebar badge count for that category in `index.html`

## Git

- Branch: `master` (not `main`)
- Remote: `https://github.com/Sunilrana1978/interview-kb.git`
- `.gitignore` excludes: `*.docx`, `*.pdf`, `*.zip`, `*.sql`, images, and Word temp files (`~$*`)
