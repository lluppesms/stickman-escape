# Project Context

- **Owner:** Lyle
- **Project:** stickman-escape — HTML5 Canvas stickman platformer with procedural level generation
- **Stack:** HTML5 Canvas, vanilla JavaScript, single-file architecture (index.html)
- **Created:** 2026-04-28

## Learnings

<!-- Append new learnings below. Each entry is something lasting about the project. -->
- 2026-04-28T18:40:02 — Environmental hazards architecture finalized and merged to decisions.md. One-pipeline system: genLevel hazard defs → spawnHazards runtime → updateHazards state machine → philTakeDmg with opts → draw layers. Three hazard types: saw (ignoresInvincibility), pit (respawnToSafe), rock (warning state machine).

