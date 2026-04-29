# Project Context

- **Owner:** Lyle
- **Project:** stickman-escape — HTML5 Canvas stickman platformer with procedural level generation
- **Stack:** HTML5 Canvas, vanilla JavaScript, single-file architecture (index.html)
- **Created:** 2026-04-28

## Learnings

### 2026-04-28: Environmental Hazards Test Plan

**Saws:**
- Saws ignore invincibility (`ignoresInvincibility: true`) — unlike enemies. They hit even during i-frames from other sources.
- No i-frame grant on saw hit — repeated hits apply every frame while overlapping.
- Saws move on axis (`x` or `y`), bounce at `min`/`max`, rotate continuously.
- Each saw is independent in collision — two saws hitting same frame = 2 damage.

**Pits:**
- Deal 2 damage, trigger respawn to `lastSafeGround` if Phil survives (health > 0).
- `lastSafeGround` tracks Phil's position only when on ground, over real platform, not dead, not overlapping hazard.
- `lastSafeGround` persists across hazard hits; pit respawn doesn't grant i-frames, so subsequent hazard hits are possible.
- Pit placement respects ground gaps; doesn't overlap spawn zone (~350px), checkpoint, or flag.
- Pit respawn fallback chain: `lastSafeGround` → `checkpointRespawn` → `playerStart`.

**Rocks:**
- Three-phase lifecycle: `idle` (waiting for Phil) → `warning` (0.45s wobble + dust) → `falling` (gravity + damage) → `spent` (done).
- Trigger on Phil entering `triggerX` range beneath rock.
- Deal 1 damage on contact while falling.
- Do not re-trigger once spent.
- Multiple rocks trigger independently in sequence.

**Integration edge cases:**
- Saw + pit + enemy damage can all apply in one frame; each source calculates independently.
- Enemy knockback can push Phil into pit/saw; both hazards apply.
- Shields don't block saws or pits.
- Pit respawn can happen while enemies are active nearby.
- Boss arenas have no procedural hazards.
- World gates: Saws World 1 level ≥7, Pits World 2 level ≥2, Rocks World 2 level ≥4.

**Performance:**
- Target max 8 hazards per level to stay smooth.
- Collision checks use AABB (no N² scaling).
- Particle system must pool/limit burst size to avoid frame drops.

**Test priority:** Damage application > respawn mechanics > movement > performance. Block release on hazard placement gates (no early saws/pits, no hazards in boss arenas).
