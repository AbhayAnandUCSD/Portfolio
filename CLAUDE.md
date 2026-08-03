# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static single-page portfolio website for Abhay Anand. No frameworks, no build tools, no dependencies — just a single `index.html` file containing all HTML, CSS, and JavaScript.

## Development

```bash
# Serve locally (pick one)
python3 -m http.server 8000
npx serve
# Or open index.html directly in a browser
```

No build, lint, or test commands exist. Deployed via GitHub Pages from the main branch.

## Architecture

Everything lives in `index.html`:

- **CSS (lines 14–270):** Embedded `<style>` block using CSS custom properties for theming. Two theme palettes (`light`/`dark`) defined on `:root[data-theme]`. Responsive breakpoint at 720px. Fluid typography via `clamp()`.
- **HTML (lines 272–410):** Three content sections — `#intro` (visible by default), `#experience` (hidden), `#projects` (hidden). Navigation uses `data-target` attributes to link to sections.
- **JS (lines 412–456):** Theme toggle persists to `localStorage`. Client-side routing toggles section visibility via the `hidden` attribute and uses `history.replaceState` for hash-based URLs. The `section-view` class on `<body>` triggers a compact header layout when viewing non-intro sections.

## Key Patterns

- **Theming:** CSS variables (`--bg`, `--text`, `--muted`, `--border`, `--link`, `--accent`) switch between light/dark via the `data-theme` attribute on `<html>`.
- **Navigation:** Clicking a nav link hides all sections except the target. Clicking the name/home link returns to `#intro`. Initial section is read from `window.location.hash`.
- **Fonts:** Google Fonts — Fraunces (serif, headings) and Inter (sans-serif, body).
