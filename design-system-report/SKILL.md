---
name: design-system-report
description: Generate a self-contained HTML design system report from a frontend codebase. Extracts color tokens, typography, component variants, brand assets, easing curves, radius scales, and glass/surface effects from CSS variables and component source files, then renders an interactive single-file report. Use when the user asks to "document the design system", "create a design report", "extract design tokens", "brand guidelines", "style guide", or wants a visual reference of their app's colors, fonts, and components.
---

# Design System Report Generator

Generate a single self-contained `index.html` that documents a frontend project's complete design system — colors, typography, components, brand assets, motion, and tokens — extracted directly from the codebase.

## When to use

- User asks for a design system report, style guide, or brand guidelines document
- User wants to visualize all their CSS tokens, colors, or component variants
- User needs a shareable reference of their app's visual language

## Workflow

### Phase 1: Extract

Gather raw design data from the codebase. Run these searches:

1. **Color tokens** — Read the main CSS file (usually `globals.css` or `index.css`) for all `--color-*` and semantic CSS custom properties (`:root` and `.dark` blocks). Capture light AND dark values.

2. **Typography** — Find `--font-*` variables and `@theme` font definitions. Check `layout.tsx` or `_app.tsx` for Google Fonts or local font imports.

3. **Brand assets** — Glob for `public/brand/**`, `public/logo*`, `public/icon*`, `public/*.svg`, `public/apple-icon*`. Read any SVG favicon to inline it.

4. **Component variants** — Read `components/ui/button.tsx`, `components/ui/badge.tsx`, `components/ui/card.tsx` for CVA variant definitions (backgrounds, borders, sizing).

5. **Radius & spacing** — Extract `--radius*` tokens from the CSS theme block.

6. **Glass/surface effects** — Find `--glass-*` tokens and any `.glass-panel` utility classes.

7. **Easing curves** — Find `--ease-*` custom properties.

8. **Chart colors** — Find `--chart-*` tokens.

9. **Sidebar tokens** — Find `--sidebar-*` tokens (if using shadcn sidebar).

### Phase 2: Build the HTML

Write a single `index.html` to `.context/report/index.html` (or user-specified path).

**Required sections** (in order):

| # | Section | Content |
|---|---------|---------|
| 1 | Header | Project name, sparkle/logo SVG inline, subtitle |
| 2 | Brand Assets | Logo variants rendered inline, asset file table |
| 3 | Core Color Tokens | Swatch grid (light), swatch grid (dark) — each swatch = colored box + hex label + token name |
| 4 | Sidebar Tokens | Light/dark table with inline color dots |
| 5 | Chart Palette | Visual color bars + table with light/dark values |
| 6 | Theme Comparison | Side-by-side light/dark card mockup showing bg/card relationship |
| 7 | Typography | Live-rendered font samples (heading, body, mono) + table |
| 8 | Radius & Spacing | Visual radius boxes + token table |
| 9 | Glass & Surface | Token table with light/dark values |
| 10 | Motion & Easing | Interactive hover-to-preview bars + token table |
| 11 | Button Component | Live variant previews + variant table + size table |
| 12 | Badge Component | Live variant previews + semantic color table |
| 13 | Card Component | Structure breakdown table |
| 14 | Full Token Reference | Complete light/dark token table with Tailwind classes |

**HTML rules:**

- **Fully self-contained** — no external CSS/JS dependencies except Google Fonts `<link>`
- **Respects `prefers-color-scheme`** — the report itself adapts to system dark/light mode using CSS variables
- **Color swatches** use actual hex values as inline `background` styles with a small monospace hex label overlay
- **Interactive easing bars** — CSS-only hover transitions that demo each curve
- **Button previews** — static HTML elements styled to match the actual component variants
- **No frameworks** — plain HTML, inline `<style>`, no build step

### Phase 3: Verify

Open the report in a browser to visually confirm it renders correctly. If `playwright-cli` is available, use it. Otherwise tell the user to open the file.

## Key patterns

### Color swatch card
```html
<div class="color-card">
  <div class="color-swatch" style="background:#3b82f6">
    <span>#3b82f6</span>
  </div>
  <div class="color-meta">
    <strong>Primary</strong>
    <code>--primary</code>
  </div>
</div>
```

### Easing preview bar (CSS-only interactive)
```html
<div class="easing-demo">
  <div class="bar" style="transition:width 500ms cubic-bezier(0.23,1,0.32,1)"></div>
</div>
```
```css
.easing-demo:hover .bar { width: 100%; }
```

### Theme comparison (side-by-side)
```html
<div class="theme-pair">
  <div class="theme-box theme-light">...</div>
  <div class="theme-box theme-dark">...</div>
</div>
```

## Output

- **File**: `.context/report/index.html` (default) or user-specified path
- **Format**: Single self-contained HTML file, ~400-800 lines
- **Fonts**: Linked via Google Fonts `<link>` tags matching the project's actual fonts
