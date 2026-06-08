# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Asteroids** es un clon del clásico arcade Asteroids implementado en **HTML5 Canvas puro**, sin dependencias ni bundler. El juego es completamente vanilla JavaScript (ES6+) en un único archivo.

### Tech Stack
- **HTML5 Canvas** — renderizado 2D
- **ES6+ JavaScript** — lógica del juego
- **No frameworks, no bundler, no dependencies**

## Quick Start

### Running the Game

**Local browser** (simplest):
```bash
open index.html
```

**With local server**:
```bash
npx serve .
```
Then visit `http://localhost:3000`

### Game Controls
- `← →` — Rotate ship
- `↑` — Thrust
- `Space` — Shoot

## Architecture

### File Structure
- `index.html` — Single HTML file with minimal styles and canvas setup
- `game.js` — All game logic (~423 lines)
- `favicon.svg` — Game icon

### Game.js Organization

The single game file is organized into clear sections:

1. **Input System** (lines 8–24)
   - `keys` object tracks currently held keys
   - `justPressed` object tracks keys pressed this frame
   - `pressed(code)` function consumes a just-pressed key

2. **Utility Functions** (lines 26–30)
   - `wrap(v, max)` — Toroidal space wrapping (edges connect)
   - `dist(a, b)` — Euclidean distance
   - `rand()` / `randInt()` — Random helpers

3. **Game Entity Classes** (lines 33–241)
   - **Bullet** — Projectiles with TTL and wrapping
   - **Asteroid** — Enemy objects with irregular polygon shapes, splits into smaller asteroids
   - **Ship** — Player-controlled vessel with rotation, thrust, and invincibility frames
   - **Particle** — Visual effects for explosions

4. **Game State Management** (lines 256–291)
   - `initGame()` — Initialize game state, reset level
   - `nextLevel()` — Spawn new asteroid wave
   - `spawnAsteroids(count)` — Create random asteroids off-screen
   - `killShip()` — Handle ship death, respawn with invincibility

5. **Game Loop** (lines 293–423)
   - `update(dt)` — Physics and collision detection
   - `draw()` — Render game state
   - `drawHUD()` — Score, lives display
   - `drawOverlay()` — Game over / level complete screens
   - `loop(ts)` — Main animation frame loop

### Key Game Constants (game.js line 5–6, 61–63)
- Canvas: 800×600 pixels
- **Asteroid Radii**: [0, 16, 30, 50] by size (1–3)
- **Asteroid Speeds**: [0, 85, 55, 32] px/s base by size
- **Points**: [0, 100, 50, 20] by size

### Physics
- **Toroidal wrapping** — objects exiting one edge appear on opposite edge
- **Collision detection** — distance between centers (see `dist()`)
- **Frame-rate independent** — all physics use `dt` (delta time in seconds)

## Development Notes

### Adding Features
- **New entity type?** Create a class with `update(dt)` and `draw()` methods
- **New game state?** Add to the main loop conditionals in `loop()`
- **UI changes?** Update `drawHUD()` or `drawOverlay()`

### Common Edits
- Adjust difficulty: modify `SPEEDS`, `RADII`, or `spawnAsteroids()` count
- Tweak game feel: change `Ship` thrust/rotation speed (line ~130–160)
- Visual tweaks: styles in `index.html` or color/size values in `draw()` methods

### Testing
Since this is a browser game with visual/input-driven behavior:
- Run locally with `npx serve . && open http://localhost:3000`
- Test controls (arrow keys, space), collisions, and level progression manually
- No automated tests; changes are verified by playing

## Scoring System

| Asteroid Size | Points |
|---|---|
| Small (size 1) | 100 |
| Medium (size 2) | 50 |
| Large (size 3) | 20 |

Destroying a large asteroid spawns 2 medium; destroying medium spawns 2 small.

## Known Behaviors
- Ship has 3 lives with temporary invincibility (blinking) on respawn
- Asteroids are irregular polygons, not circles (generated randomly per asteroid)
- Bullets wrap around screen edges
- Particles emit on asteroid destruction for visual feedback
- Next level spawns when all asteroids are destroyed
