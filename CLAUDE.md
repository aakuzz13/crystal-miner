# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository overview

This repo contains a single self-contained HTML5 clicker game, **"Кристальная Шахта 2.0"** (Crystal Mine 2.0) — a Russian-language idle/clicker game in the style of Cookie Clicker. There is no build system, no package manager, and no dependencies beyond a Google Fonts import (`Cinzel`).

- `miner-clicker-v2.html` — the entire game: HTML structure, CSS (inline `<style>`), and JavaScript (inline `<script>`), all in one file.

## Running / testing

There is no build step. To work on the game, just open `miner-clicker-v2.html` directly in a browser, or serve the directory with any static file server, e.g.:

```
python3 -m http.server 8000
```

then visit `http://localhost:8000/miner-clicker-v2.html`. There are no automated tests or linters configured — verify changes manually in a browser.

## Architecture

Everything lives in one IIFE inside the `<script>` tag at the bottom of `miner-clicker-v2.html`. Key pieces, in order:

- **State**: a single `state` object (resources, totalEarned, clickLevel, autoMiners array, currentLocation, muted) persisted to `localStorage` under `SAVE_KEY` via `save()`/`load()`. A separate `SAVE_KEY + "_ts"` timestamp is used to compute offline progress on load (`applyOfflineProgress()`, capped at 4 hours).
- **Static game data**: `clickUpgrade` (the pickaxe upgrade), `autoDefs` (array of 4 auto-miner definitions with cost/rate/rarity), `LOCATIONS` (array of 5 unlockable locations, each with its own color theme and `unlockAt` threshold based on `totalEarned`), and `RARITY` (label/icon per rarity tier: common/rare/epic/legendary).
- **Economy math**: `costFor()` computes exponential upgrade costs (`base * mult^level`); `clickPower()` and `perSecond()` compute current income, both scaled by `locationBonusMult()` (+15% per unlocked location beyond the first).
- **Audio**: a small synth built on the Web Audio API (`tone()`, `sweep()`) with no external audio files — `playClick()`, `playBuy()`, `playTravel()`, `playUnlock()` compose short procedural sound effects, gated by `state.muted`.
- **Rendering**: DOM is built imperatively (no framework). `renderPanel()` dispatches to `renderClickTab()` / `renderAutoTab()` / `renderLocTab()` based on the active nav tab, rebuilding the `#panel` contents from the static data + current state on every state change. `buildMinerCard()` and `buildLocationCard()` construct the card elements.
- **Visual effects**: floating "+N" numbers and particle "chip" bursts on click/purchase (`spawnFloatNum`, `spawnChips`, `burstAtViewportPoint`, `celebrateBuy`), ambient background particles (`spawnAmbient`/`startAmbient`), and a radial "portal" wipe transition when switching locations (`travelTo()`). All effects respect `prefers-reduced-motion` (checked via `reducedMotion` and mirrored in a CSS `@media (prefers-reduced-motion: reduce)` block).
- **Game loop**: a `setInterval` every 200ms adds accumulated passive income (`perSecond() * dt`); a separate interval autosaves every 5s; `beforeunload` also saves and stamps the offline-progress timestamp.
- **Locations as a theming mechanism**: switching `state.currentLocation` doesn't just change flavor text — `applyLocationVisual()` rewrites CSS custom properties (`--accent`, `--rockBase`, etc.) on `#rock-wrap` and the page background, so the rock/crystal SVG and ambient particles change color per-location live.

When editing, keep everything within the single HTML file — this project intentionally has no build pipeline or module system.
