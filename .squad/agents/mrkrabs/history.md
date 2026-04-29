# Project Context

- **Owner:** Lyle
- **Project:** stickman-escape — HTML5 Canvas stickman platformer with procedural level generation
- **Stack:** HTML5 Canvas, vanilla JavaScript, single-file architecture (index.html)
- **Created:** 2026-04-28

## Learnings

- **Cheat code system:** Two input-based cheats controlled by sequence buffers with 1.4s timeout between keypresses. Keyboard (`xavier` = world skip, `drum` = level skip), mobile/touch via button sequences (`jjsjjsjjs` = world skip, `rrrjjj` = level skip). Cheats only active during PLAYING state, never during paused or final boss encounters. Both cheats restore player state (lives/health) and preserve score.
- **Touch controls:** Five buttons (◀ Left, ▶ Right, ▲ Jump, ⚔ Attack, 🛡 Shield). Only R, J, and S buttons trigger cheat input handlers. Touch layer hidden on non-touch devices and positioned at bottom of screen (160px height).
- **Documentation:** Created standalone `cheat-codes.md` in repo root (separate from README) for discoverable cheat code reference.
