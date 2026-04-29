# Decisions

## Bone Revenant split damage fix

**Context:** The Bone Revenant already exposes each live twin through the shared boss targeting helpers, but split-phase damage was blocked because `hitBoss()` bailed out when `boss.hidden` was true and still treated health as one shared pre-split pool.

**Decision:** For the Revenant split phase, keep using the shared boss targeting helpers, but switch split resolution to twin-driven state:
- each live twin is a valid hit target
- a hit immediately marks that twin dead
- split health tracks remaining live twins (`2 -> 1 -> 0`)
- defeating both twins ends the fight

**Why:** This fixes the bug without creating a separate collision system just for one boss, and it preserves the existing boss-contact / camera-focus helpers that already understand live twin bodies.

---

## Level Skip Cheat

**Decision:** Use `drum` for the keyboard level-skip cheat and `sjs` for the mobile pattern.

**Why:**
- `drum` is short, memorable, and distinct from the existing `xavier` world cheat.
- `sjs` only uses buttons available on mobile and is less likely to fire accidentally than `jsj` during normal movement.
- The implementation reuses the existing keyboard/touch cheat entry points so world skip and level skip stay consistent, while keeping separate buffers/timeouts so the two codes do not interfere with each other.

**Behavior:**
- Skip exactly one level.
- Level 10 rolls into the next world's level 1.
- World 6 level 10 routes into the final boss.
- Refill health and reset lives to 3.
- Play clear feedback when the cheat lands.
