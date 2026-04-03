# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

"The Daily Diamond" is a static MLB box scores viewer. The entire application lives in a single file: `public/index.html`. There is no build step, no framework, and no package manager — just vanilla HTML, CSS, and JavaScript deployed to GitHub Pages.

## Development

Open `public/index.html` directly in a browser. Edit and refresh. That's it.

Deployment is automatic via `.github/workflows/pages.yaml` on push to `main`.

## Architecture

The entire app is ~920 lines in `public/index.html`:

- **~510 lines of CSS** — uses CSS custom properties (`--bg`, `--paper`, `--ink`, `--accent`, etc.) for theming. Responsive via CSS Grid and media queries.
- **~375 lines of vanilla JavaScript** — no frameworks, no bundler. Uses Fetch API, native `<dialog>` for modals, and `document.createDocumentFragment()` for DOM updates.

### Data Flow

1. Page loads → `loadDay(dateStr)` fetches schedule from MLB Stats API (`https://statsapi.mlb.com/api/v1`)
2. Game cards rendered from schedule data
3. Click a card → `openDialog(game)` opens a native `<dialog>` modal
4. `loadBoxScore()` fetches detailed stats and renders batting/pitching tables
5. Click a player name → `openPlayerGameLog()` fetches season game log; back button returns to box score

### Global State

Three global variables track UI state:
- `currentBoxScoreGame` — game currently displayed in modal
- `currentBoxScoreDate` — date of current box score
- `currentGameType` — filter for game type (R=regular, S=spring, E=exhibition, etc.)

### Patterns

- Event delegation on the dialog body handles clicks on dynamically created player links
- All MLB data is fetched live from the public MLB Stats API — no auth required
- `ScoreGrid` component (restored via `restore scroegrid on preview boxes`) renders the linescore grid
