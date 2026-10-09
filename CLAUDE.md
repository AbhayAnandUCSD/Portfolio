# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Static personal site for Abhay Anand. No frameworks, no build tools, no dependencies —
hand-written HTML with one shared stylesheet.

## Development

```bash
# Serve locally (pick one)
python3 -m http.server 8000
npx serve
```

Open `http://localhost:8000/`. There is no build, lint, or test step.
Deployed via Vercel from the main branch.

## Structure

```
index.html      home — the bio, with the identity rail (photo, name, links)
projects.html   projects grid, full width, no rail
notes.html      writing index, full width, no rail
style.css       every style for all three pages
media/          preview clips: <name>_tile.{mp4,webm,webp}
assets/         abhay.jpg
simpleresume/   a standalone note, linked from notes.html
vercel.json     redirects + rewrites (see below)
```

## Editing content

Both lists are plain JS arrays at the bottom of their page, marked with an
`// ---- edit X here` comment. No templating.

- **Projects** — `projects.html`, the `PROJECTS` array. Each entry is
  `{ title, link, href, desc, media }`, where `media` is a path prefix like
  `/media/robot_tile` and the page appends `.webp` / `.webm` / `.mp4`.
  Reorder by moving whole blocks.
- **Notes** — `notes.html`, the `NOTES` array: `{ title, date, href }`.
- **Bio** — `index.html`, the `.excerpt` block, plain paragraphs.

## Key patterns

- **Theming.** CSS custom properties (`--bg`, `--surface`, `--text`, `--muted`,
  `--border`, `--accent`) swap on `:root[data-theme]`. An inline script in every
  `<head>` reads `localStorage.theme` before first paint, so there is no flash;
  the toggle persists the choice and it carries across pages.
- **Layout.** Home uses a two-column grid — a sticky 340px rail beside the
  content. Projects and Notes drop the rail for a centred 1060px column. The
  projects grid collapses to one column at 900px; the rail stacks at 768px.
- **Preview clips.** Each project card holds a muted, looping `<video>` with
  `preload="none"` and a `.webp` poster, so nothing downloads until hover.
  Hover (or keyboard focus) plays it and pauses+rewinds on leave; touch devices
  have no hover, so an IntersectionObserver plays whichever card is centred.
  `prefers-reduced-motion` suppresses playback and leaves the poster.
- **Media URLs** carry a `?v=<timestamp>` cache-buster, because the clips get
  re-encoded in place and browsers otherwise hold stale copies.

## Encoding new preview clips

Tiles are 16:10. Match the existing files exactly:

```bash
ffmpeg -i in.mp4 -an -vf "scale=720:-2:flags=lanczos" \
  -c:v libx264 -crf 26 -preset slow -pix_fmt yuv420p -movflags +faststart out_tile.mp4
ffmpeg -i in.mp4 -an -vf "scale=720:-2:flags=lanczos" \
  -c:v libvpx-vp9 -crf 33 -b:v 0 -row-mt 1 -pix_fmt yuv420p out_tile.webm
ffmpeg -ss 3 -i in.mp4 -frames:v 1 -pix_fmt rgb24 poster.png
cwebp -q 84 poster.png -o out_tile.webp     # ffmpeg's webp encoder is disabled locally
```

Keep tiles under ~750KB. Pixel art needs `flags=neighbor` and a lower crf.

## The Research Map note (spacing + source of truth)

`notes/researchmap/index.html` has a second copy at
`~/GithubFolders/IdeaMap/site/note-researchmap.html`, and sessions working on
IdeaMap copy that file over this one. A stale copy there silently reverts edits
made here (it already undid the width fix and the rename twice). Edit **both**,
or copy this file back into IdeaMap, before committing.

Spacing the note must keep (laptop):

- `.note-body { max-width: none }` — prose spans the full 1060px column so it
  lines up with the map card. Do not restore the old `34em` cap.
- `.note-body p` is `1.2rem` / `line-height: 1.75` (`1.06rem` under 768px).
- Title/h1/Notes entry read "Getting started with Research" (lowercase "started").

## vercel.json

Routes `/bookofworlds` to the Book of Worlds site on GitHub Pages (repo
`abhayananducsd/bookofworlds`) and proxies `/bookofworlds/api/*` to its backend.
The old `/generalintuition` paths permanently redirect to `/bookofworlds`.
