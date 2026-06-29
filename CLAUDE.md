# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running

No build step. Open directly or serve statically:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Or just open `index.html` in a browser.

## Architecture

Three files, all logic in `game.js`:

- **`index.html`** — DOM structure: `<canvas id="board">` (300×600px), sidebar panel (score/lines/level/next-piece preview), and `#overlay` div for pause/game-over states.
- **`style.css`** — Dark retro theme. `.overlay.hidden` toggles game states. No JS class logic.
- **`game.js`** — All game logic (~300 lines, `'use strict'`, no modules, no dependencies).

### Key data structures

- `board`: `ROWS×COLS` matrix; cells are `0` (empty) or `1–7` (piece color index).
- `current` / `next`: `{ type, shape, x, y }` where `shape` is a 2D matrix.

### Key functions

| Function | Role |
|---|---|
| `collide(shape, ox, oy)` | Bounds + overlap check against `board` |
| `rotateCW(shape)` | Transpose + row-reverse (clockwise rotation) |
| `tryRotate()` | Rotates with wall kicks `[0, -1, 1, -2, 2]` |
| `clearLines()` | Splices full rows, updates score/level/speed |
| `ghostY()` | Projects current piece to landing row |
| `loop(ts)` | `requestAnimationFrame` game loop; accumulates `dt` against `dropInterval` |
| `init()` | Full reset; called on load and restart |

### Tunable constants in `game.js`

| Constant | Default | Note |
|---|---|---|
| `COLS` / `ROWS` | 10 / 20 | Must match canvas `width`/`height` in HTML (`COLS×BLOCK` / `ROWS×BLOCK`) |
| `BLOCK` | 30 | Pixels per cell |
| `LINE_SCORES` | `[0,100,300,500,800]` | Points for 1–4 line clears (multiplied by level) |
| Speed formula | `max(100, 1000 − (level−1) × 90)` ms | In `clearLines()` |
