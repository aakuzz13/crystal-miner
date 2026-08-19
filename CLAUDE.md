# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository overview

This repo contains a Russian-language HTML5 idle/clicker game, **"Кристальная Шахта"**, in the style of Cookie Clicker. There is no build system, no package manager. Multiple versions of the game coexist side by side:

- `versions/miner-clicker-v2.html` — Cookie-Clicker-style version. Self-contained: HTML + inline `<style>` + inline `<script>`, no external assets besides a Google Fonts import (`Cinzel`). Frozen — do not edit.
- `versions/miner-clicker-v3.html` — current version, redesigned around an Inscryption-inspired art direction (etched/woodcut linework, parchment cards, wax-seal rarity marks, film grain + vignette) with a gacha chest/reel mechanic layered on top of the same base economy as v2. Loads real assets from `/assets` via relative paths (`../assets/...`), so it must be served over HTTP, not opened via `file://`.
- `assets/sprites/`, `assets/sounds/` — third-party CC0 assets (Kenney.nl) used by v3. See `docs/credits.md` for what's from where and its license.
- `docs/credits.md` — asset attribution/licensing ledger; update it whenever a new third-party asset is added.

## Running / testing

There is no build step. For v2, opening the file directly in a browser works. For v3, serve the repo root over HTTP (it fetches sounds/sprites by relative path):

```
python3 -m http.server 8000
```

then visit `http://localhost:8000/versions/miner-clicker-v3.html` (or `miner-clicker-v2.html`). There are no automated tests or linters configured — verify changes manually in a browser. Playwright + a pre-installed Chromium are available in this environment for scripted browser checks (see the `run` skill) if you need to verify interactions headlessly — note the gacha chest has a CSS bob animation, so Playwright needs `click({force: true})` on `#chest`, and forcing a specific gacha outcome for testing means overriding `Math.random` via `page.addInitScript` (be aware `buildGrainTile()` and `generateLocationTexture()` use a seeded `mulberry32` PRNG, not `Math.random`, specifically so they don't consume calls from the global random stream).

## Architecture (shared by v2 and v3)

Everything lives in one IIFE inside the `<script>` tag at the bottom of each version's HTML file. Key pieces, in order:

- **State**: a single `state` object persisted to `localStorage` under `SAVE_KEY` via `save()`/`load()`. v2 and v3 use different `SAVE_KEY` values so progress never crosses versions. A separate `SAVE_KEY + "_ts"` timestamp computes offline progress on load (`applyOfflineProgress()`, capped at 4 hours).
- **Static game data**: `clickUpgrade` (the pickaxe upgrade), `autoDefs` (array of 4 auto-miner definitions with cost/rate/rarity), `LOCATIONS` (array of 5 unlockable locations, each with `unlockAt` based on `totalEarned`), and `RARITY`. **Economy numbers here (`baseCost`, `costMult`, `rate`, `unlockAt`) are identical between v2 and v3 by design** — v3 only changes acquisition/presentation, not the underlying curve.
- **Economy math**: `costFor()` computes exponential upgrade costs (`base * mult^level`); `clickPower()` and `perSecond()` compute current income, both scaled by `locationBonusMult()` (+15% per unlocked location beyond the first).
- **Rendering**: DOM is built imperatively (no framework). `renderPanel()` dispatches to `renderClickTab()` / `renderAutoTab()` / `renderLocTab()` based on the active nav tab, rebuilding `#panel` from static data + state on every change. `buildMinerCard()` / `buildLocationCard()` construct card elements.
- **Visual effects**: floating "+N" numbers, particle bursts, ambient background particles, and a radial "portal" wipe transition on location travel (`travelTo()`). All effects respect `prefers-reduced-motion`.
- **Game loop**: `setInterval` every 200ms adds accumulated passive income; a separate interval autosaves every 5s; `beforeunload` also saves and stamps the offline-progress timestamp.
- **Locations as a theming mechanism**: switching `state.currentLocation` rewrites CSS custom properties (`--accent`, `--rockBase`, etc.) via `applyLocationVisual()`, so the rock/crystal art and ambient particles change color per-location live.

## v3-specific additions

- **Auto-miner acquisition has two independent tracks that share the same base economy.** `state.shopCounts[i]` mirrors v2 exactly — buying with resources via `costFor(autoDefs[i].baseCost, autoDefs[i].costMult, shopCounts[i])` adds a flat-rate instance. `state.gachaMiners` is a separate array of `{defIndex, rate}` — each entry is a *free* random drop from a gacha chest, with `rate` individually rolled at `autoDefs[defIndex].rate * (0.8..1.2)` (`STAT_ROLL_RANGE`). `perSecond()`/`minerRateFor()` sum both tracks. Never conflate the two or change `costFor`'s inputs — that curve is the "preserve the economy" contract.
- **Chest/gacha flow**: `maybeDropChest()` rolls `CHEST_DROP_CHANCE` on every ore click; the chest sprite (`#chest`) must carry the `hidden` class by default in the HTML (its CSS bob animation makes a missing `hidden` class easy to miss visually during review, but it means the chest silently sits pre-spawned). Clicking it calls `openGachaFlow()`: `rollRarity()` weights outcomes via `GACHA_WEIGHTS`, then `autoDefs.findIndex(d=>d.rarity===rarity)` maps rarity straight to a def (each of the 4 defs currently owns one distinct rarity tier, so this mapping is 1:1 — if that ever changes, `rollRarity`'s def-lookup needs to become def-index-weighted instead of rarity-weighted). The reel (`#reel-track`) animates via `requestAnimationFrame` with an eased offset (`1 - (1-t)^power`, higher power for legendary), ticking a sound sample every time the floor'd item index changes.
- **Ore damage state** (`oreStage`, 0→1→2→shatter-reset) is purely a cosmetic click counter, not persisted — every click always grants `clickPower()` regardless of stage; the stage only toggles which crack `<g>` layer is visible and triggers a bigger burst + flash on the 3rd hit.
- **Procedural location textures**: `generateLocationTexture(loc, size)` renders per-location canvas noise (coarse + fine value noise via a seeded `mulberry32` PRNG, not `Math.random`) into a cached data URL, applied as an SVG `<pattern>` fill on the rock polygon (`#rockTextureImg`) — this is what gives the rock itself a location-specific stone texture instead of a flat gradient. Cached per `loc.id + size` in `textureCache`; regenerate-on-demand, not persisted to `localStorage`.
- **Audio**: real CC0 sample playback (`playSample()`, fetched + `decodeAudioData`'d into `sampleCache`, routed through a shared generated-impulse-response `ConvolverNode` for cave reverb) replaces most of v2's oscillator-only approach; a few oscillator-based tones (`tone()`) remain for lightweight UI feedback, now routed through a lowpass filter to stay "duller" than v2's chiptune tones.
- **Third-party assets are referenced by relative path from `versions/`** (`../assets/sprites/...`, `../assets/sounds/...`) — moving `miner-clicker-v3.html` out of `versions/` without updating those paths (or the sibling `assets/` layout) will break it silently (images/audio just fail to load; there's no visible error in the UI).

When editing either version, keep everything within its single HTML file (v3's JS may be split into a sibling `.js` file if it grows unwieldy, per the file's own header comment intent, but is currently inline) — this project intentionally has no build pipeline or module system.
