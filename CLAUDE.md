# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Tetris in vanilla JavaScript + HTML5 Canvas. No dependencies, no package.json, no build step, no tests, no linter. The README and UI text are in Spanish; keep user-facing strings in Spanish.

## Running

Open `index.html` directly in a browser, or serve the folder (e.g. `npx serve .` or `python3 -m http.server 8000`) and visit `http://localhost:3000` or `http://localhost:8000`.

## Architecture

Three files, all loaded by `index.html` (`game.js` is a classic script, not a module):

- `index.html` — static markup: the `#board` canvas (300×600 = 10×20 cells at 30px), the `#next-canvas` preview (120×120), HUD spans (`#score`, `#lines`, `#level`), and the `#overlay` used for both pause and game over.
- `style.css` — layout/theme only.
- `game.js` — the entire game, using module-level mutable globals (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, ...) declared in one `let` and initialized by `init()`.

Key game.js flow:

- `init()` resets all state and starts the `requestAnimationFrame` `loop`; the restart button calls it again.
- `loop(ts)` accumulates elapsed time in `dropAccum` and applies gravity when it exceeds `dropInterval`, then calls `draw()`.
- Input is a single `keydown` listener (arrows, `X` rotate, `Space` hard drop, `P` pause).
- Piece lifecycle: `lockPiece()` → `merge()` → `clearLines()` → `spawn()`. `spawn()` promotes `next` to `current` and ends the game if the new piece collides immediately.
- Pieces are 2D matrices in `PIECES`; cell values 1–7 double as indices into `COLORS`, and the board stores those same values (0 = empty). `collide(shape, ox, oy)` is the single collision check used by movement, rotation (`tryRotate` with horizontal wall kicks `[0,-1,1,-2,2]`), ghost piece and spawn.
- Scoring: `LINE_SCORES[cleared] * level`; soft drop +1/cell, hard drop +2/cell; level = `floor(lines/10)+1`; `dropInterval = max(100, 1000 - (level-1)*90)` ms.
- Pause and game over both stop the rAF loop via `cancelAnimationFrame(animId)` and show `#overlay`; unpausing restarts `loop` with a fresh `lastTime`.
