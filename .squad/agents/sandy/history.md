# Project Context

- **Owner:** Lyle
- **Project:** stickman-escape — HTML5 Canvas stickman platformer with procedural level generation
- **Stack:** HTML5 Canvas, vanilla JavaScript, single-file architecture (index.html)
- **Created:** 2026-04-28

## Learnings

<!-- Append new learnings below. Each entry is something lasting about the project. -->
- Hazard visuals live entirely in `index.html`; keep rendering-only polish in helper functions above `drawHazards()` so SpongeBob's hazard logic/data stays untouched.
- 2026-04-28T18:40:02 — Hazard rendering split: drawHazardsBack() for static/background (saw tracks, pit fills, rock anchors), drawHazardsFront() for dynamic/character-layer pieces (moving blades, falling rocks). Draw order: platforms → decorations → hazardsBack → flags/boss → entities → Phil → hazardsFront → particles/HUD. Pits must read embedded; blades/rocks read clearly over characters.

