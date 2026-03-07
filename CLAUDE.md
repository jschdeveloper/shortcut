# CLAUDE.md — AI Assistant Guide for `shortcut`

## Project Overview

`shortcut` is a **single-file, static HTML browser start page** — a personal productivity dashboard that displays a grid of links to frequently visited web services. There are no build steps, no package managers, and no external dependencies beyond CDN-served icons.

- **Primary file:** `index.html` (all HTML, CSS, and JS in one file, ~945 lines)
- **Local assets:** `iconos/` directory (33 custom SVG icons)
- **Deployment:** GitHub Pages (static serving of `index.html` directly)
- **No framework, no build tool, no test suite**

---

## Repository Structure

```
shortcut/
├── index.html        # Entire application (HTML + inline CSS + inline JS)
├── iconos/           # 33 custom SVG icon files (local fallbacks)
├── shorcut.png       # Project screenshot (used in README)
└── README.md         # Minimal project description
```

---

## Architecture: `index.html`

Everything lives in `index.html`. The file is divided into three logical sections:

### CSS (lines 6–377 + 642–943)
- All styles are inline `<style>` blocks
- Theming uses **CSS custom properties** (`var(--bg-color)`, `var(--card-bg)`, `var(--text-color)`, `var(--card-size)`, etc.)
- Dark theme is the default; light theme is applied by toggling `.light-theme` on `<body>`
- Responsive breakpoint at `768px`
- Grid uses `grid-template-columns: repeat(auto-fill, minmax(var(--card-size), 1fr))`

### HTML Structure (lines 380–640)
- `<header class="header">`: Fixed top bar containing:
  - 9 timezone clocks (Barcelona, México, Argentina, Chile, Polonia, Francia, Filipinas, Italia, Portugal)
  - Countdown timer to 18:00 (workday end)
  - Search box with real-time filtering
  - Card size controls (+/−)
  - Dark/light theme toggle
- `<div class="grid-container">`: Grid of `<a>` shortcut cards (35 total)

### JavaScript (lines 684–905)
All JS is in a single inline `<script>` block. Key functions:

| Function | Purpose |
|---|---|
| `toggleTheme()` | Switches dark/light, persists to `localStorage` |
| `adjustCardSize(change)` | Resizes cards ±10px (clamped 60–180px), persists to `localStorage` |
| `updateTimes()` | Updates 9 timezone displays every second |
| `updateCountdown()` | Countdown to 18:00, changes emoji/color at <30 min |
| `handleSearch(event)` | Real-time card filtering by title/alt/URL |
| `openFirstMatch()` | Opens first search result on Enter or button click |
| `adjustContentMargin()` | Recalculates grid top margin based on dynamic header height |
| `trackUsage(url)` | Placeholder for analytics (currently not implemented) |

**State persistence:** `localStorage` stores `theme`, `cardSize`, and flag image dimensions.

**Keyboard shortcuts:**
- `Ctrl+Space` — focus the search input

---

## Shortcut Cards

Each card is an `<a>` tag inside `.grid-container`:

```html
<a href="https://example.com" target="_blank" title="Service Name">
  <img class="grid" src="icon-url-or-local-path" alt="Service Name">
  <p>Service Name</p>
</a>
```

### Icon Sources (in priority order)
1. **CDN:** `jsDelivr` serving `simple-icons` SVGs (v3 and v9)
2. **CDN:** `cdnjs` serving `simple-icons` v3.2.0
3. **Local:** `/iconos/filename.svg` — custom or override icons
4. **Flags:** `flagcdn.com` for country flag images

### Dark Mode Icon Handling
Icons not natively suited for dark backgrounds use CSS `filter: invert(1)` or `filter: brightness(...)` — applied selectively per card.

---

## Conventions to Follow

### When Adding a New Shortcut Card
1. Add an `<a>` block inside `<div class="grid-container">` following the existing pattern
2. Prefer CDN icon sources; use `/iconos/` only for icons unavailable on CDN
3. Always include `target="_blank"`, a meaningful `title`, and a matching `alt` on the `<img>`
4. Keep `<p>` label short (fits under a small card)

### When Modifying CSS
- Use existing CSS variables; do not hardcode colors or sizes
- New theme-dependent styles should use `var(--...)` properties
- Add dark/light variants via the `.light-theme body` selector pattern

### When Modifying JavaScript
- Keep all JS in the existing `<script>` block — do not create external `.js` files
- Use `localStorage` for any new persistent settings
- Do not introduce framework or library dependencies

### General
- This is a **single-file application** — resist the urge to split into multiple files
- No build step exists; changes to `index.html` are immediately deployable
- No linter or formatter is configured; match the existing code style

---

## Git Workflow

- **Default branch:** `master`
- **Deployment branch:** `gh-pages` (mirrors master for GitHub Pages)
- **Feature branches:** Use the `claude/[description]-[sessionId]` naming convention
- **Merging:** PRs are merged into `master`, then `gh-pages` is kept in sync
- **Commit signing:** SSH-based signing is configured

```bash
# Standard development cycle
git checkout -b claude/feature-description-sessionId
# ... make changes to index.html ...
git add index.html
git commit -m "Descriptive message"
git push -u origin claude/feature-description-sessionId
```

---

## No Build, No Tests

- There is **no `npm install`**, no `package.json`, no bundler
- There are **no tests** — validate changes by opening `index.html` in a browser
- There is **no linter** — follow existing code style manually

---

## Key External Dependencies (CDN only)

| Library | Version | Used For |
|---|---|---|
| simple-icons | v3, v3.2.0, v9 | Brand SVG icons |
| flagcdn.com | latest | Country flag images |

No local npm packages. No lock file. No node_modules.
