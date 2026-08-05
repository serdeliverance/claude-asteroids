# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file Asteroids clone in vanilla JavaScript, rendered on an HTML5 `<canvas>`. No build step, no bundler, no dependencies.

## Running

```bash
npx serve .
```

Then visit `http://localhost:3000`. Alternatively, open `index.html` directly in a browser. There is no test suite, linter, or build/compile step — verify changes by running the game in a browser.

## Architecture

Everything lives in `game.js` (single file, ~420 lines), loaded by `index.html` into an 800×600 canvas.

- **Game loop**: `requestAnimationFrame` drives `update(dt) → draw()` each frame. `dt` (seconds) is capped at 0.05 to avoid physics jumps after tab-throttling.
- **Input**: `keys` (held) and `justPressed`/`pressed()` (edge-triggered, e.g. for single-shot fire on Space) are populated by keydown/keyup listeners.
- **Entities**: `Ship`, `Bullet`, `Asteroid`, `Particle` are classes, each with `update(dt)` and `draw()`. All positions wrap toroidally via the `wrap()` helper — the play field has no walls.
- **State machine**: global `state` is one of `'playing' | 'dead' | 'gameover'`, checked at the top of `update()` to branch behavior (respawn delay, game-over restart-on-Space, etc.).
- **Asteroids**: spawn at size 3, split into two size-(n-1) asteroids on hit via `split()`, disappear at size 1. Each instance generates its own irregular polygon (`verts`) at construction. Speed and points-per-kill are inverse to size (`SPEEDS`, `POINTS` arrays indexed by size).
- **Collisions**: simple distance checks (`dist()` vs summed radii) each frame in `update()` — bullet-vs-asteroid (splits asteroid, adds score, spawns explosion particles) and ship-vs-asteroid (skipped while `ship.invincible > 0`, e.g. right after spawn/respawn).
- **Level progression**: when `asteroids.length === 0`, `nextLevel()` increments `level` and spawns `3 + level` new size-3 asteroids.

Comments and UI text in the code are in Spanish (e.g. `NIVEL`, `PUNTAJE`); keep new comments/strings consistent with that if editing player-facing text.

Note: `README.md` currently describes power-ups and a "shooting star" asteroid type that do not exist in `game.js` (removed in a prior commit) — don't treat the README as a source of truth for current features.
