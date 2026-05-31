# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A single-file browser Snake game (`snake.html`). No build step, no dependencies, no package manager — open the file directly in a browser to run it.

## Running the game

```powershell
Start-Process "snake.html"
```

Or double-click `snake.html` in Explorer.

## Git workflow

`git` is installed at `C:\Program Files\Git\bin\git.exe` and is **not** on the system PATH by default. Prepend it each session:

```powershell
$env:PATH += ";C:\Program Files\Git\bin"
```

**Every meaningful change must be committed and pushed.** After editing any file, run:

```powershell
$env:PATH += ";C:\Program Files\Git\bin"
git add <changed-files>
git commit -m "<concise description of what changed and why>"
git push
```

This keeps GitHub in sync so any version can be reverted to at any time. Use `gh auth setup-git` if the push returns a 401.

## Architecture

Everything lives in `snake.html` as three sections:

- **CSS** (`<style>`) — dark theme, overlay panel (start / pause / game-over), scoreboard.
- **HTML** — a `<canvas id="canvas">` (480×480 px) with an absolutely-positioned `#overlay` div stacked on top for menus.
- **JS** (`<script>`) — pure vanilla, no frameworks.

### Game loop

The grid is 24×24 cells (each 20 px). `setInterval(step, speed())` drives the game. Each tick:
1. Advance the head by `dir`.
2. Check wall/self collision → `endGame()`.
3. `unshift` the new head; `pop` the tail (or keep it on food).

`nextDir` buffers the player's last keypress so mid-tick key presses don't cause a 180° reversal.

### Speed scaling

`speed() = max(60, 130 − score × 2)` — interval shrinks by 2 ms per food eaten, floored at 60 ms.

### High score

Persisted in `localStorage` under the key `snakeBest`.
