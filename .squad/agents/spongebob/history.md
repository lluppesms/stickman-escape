# Project Context

- **Owner:** Lyle
- **Project:** stickman-escape — HTML5 Canvas stickman platformer with procedural level generation
- **Stack:** HTML5 Canvas, vanilla JavaScript, single-file architecture (index.html)
- **Created:** 2026-04-28

## Learnings

<!-- Append new learnings below. Each entry is something lasting about the project. -->
- 2026-04-28T18:40:02.548-05:00 — Environmental hazards in `index.html` are a dedicated subsystem: `genLevel()` returns `hazards`, `spawnHazards()` hydrates runtime state, `updateHazards(dt)` runs after pickups and before camera, and `drawHazards()` renders between decorations and flags.
- 2026-04-28T18:40:02.548-05:00 — Hazard-safe recovery uses `lastSafeGround`, seeded from `levelData.playerStart`, refreshed only from grounded safe positions, and reset again inside `respawnPhil()` to avoid stale pit respawns.
- 2026-04-28T18:40:02.548-05:00 — World hazard gates are encoded directly in `index.html`: World 1 late levels add moving saws, World 2 adds lava pits and then falling rocks, and boss arenas intentionally keep `hazards: []`.
- 2026-04-28T19:07:29.550-05:00 — Cheat-code input in `index.html` now fans out through the same keyboard/touch entry points for both world-skip and level-skip cheats, but each cheat keeps its own buffer and timeout state.
- 2026-04-28T19:07:29.550-05:00 — Level-skip cheats should mirror a normal clear transition: advance exactly one level, roll level 10 into the next world or final boss, refill Phil to full health, reset lives to 3, and give clear-SFX feedback.
