# Test Plan: Environmental Hazards

**Date:** 2026-04-28  
**Tester:** Patrick  
**Reference:** `.squad/decisions/inbox/squidward-hazards-architecture.md`

---

## Overview

This test plan covers environmental hazards (saws, pits, rocks) introduced to the game. The test categories below validate collision mechanics, damage application, state transitions, and edge cases that could break core gameplay.

---

## 1. SAW HAZARDS

### 1.1 Damage Application
**Test:** SAW-DMG-001  
**Condition:** Phil walks directly into a moving saw blade  
**Expected:** Phil takes exactly 1 heart damage, no more  
**Verify:**  
- `phil.health` decreases by 1  
- Burst particle effect appears at Phil's position  
- SFX.hit() plays  

---

**Test:** SAW-DMG-002  
**Condition:** Phil stands in saw path and gets hit repeatedly  
**Expected:** Damage applies every frame Phil overlaps the saw (saw ignores invincibility)  
**Verify:**  
- First hit: health = 4 (started at 5)  
- No invincibility window blocks second hit in next frame  
- Health continues to drop while standing in saw path  
**Note:** Unlike enemy hits, saws do NOT grant i-frames  

---

**Test:** SAW-DMG-003  
**Condition:** Phil has invincibility from an ENEMY hit, then walks into a saw  
**Expected:** Saw damage applies immediately (invincibility bypass)  
**Verify:**  
- Enemy hit Phil → `invTimer = 1.1s`  
- While `invTimer > 0`, Phil walks into saw  
- Saw damage applies despite active `invTimer`  
- `invTimer` remains unchanged (saw does not reset it)  

---

### 1.2 Saw Movement
**Test:** SAW-MOVE-001  
**Condition:** Saw is placed on horizontal track with `axis:'x'`, `min:100`, `max:500`, `speed:200`  
**Expected:** Saw moves left/right within bounds, bounces at endpoints  
**Verify:**  
- At frame N: `x = 100` (min boundary)  
- Saw moves right: `dir = 1`  
- At frame M: `x` approaches `500`  
- At bounce: `dir` flips to `-1`  
- Saw moves left back toward `100`  
- Movement is smooth and continuous  

---

**Test:** SAW-MOVE-002  
**Condition:** Saw is placed on vertical track with `axis:'y'`, `min:150`, `max:400`  
**Expected:** Saw moves up/down within bounds, bounces at endpoints  
**Verify:**  
- `y` oscillates between `min` and `max`  
- Speed is consistent on each bounce  
- No overshoot past boundaries  

---

**Test:** SAW-MOVE-003  
**Condition:** Saw rotation property updates during movement  
**Expected:** `rot` property increments every frame (visual spin)  
**Verify:**  
- `rot` increases continuously (used for canvas rotation)  
- Rotation does not affect collision geometry  

---

### 1.3 Saw Collision Edge Cases
**Test:** SAW-COLL-001  
**Condition:** Phil jumps OVER a moving saw blade  
**Expected:** No damage if Phil clears the saw completely  
**Verify:**  
- Phil's bounding box must not overlap saw's bbox  
- If no overlap, no damage  

---

**Test:** SAW-COLL-002  
**Condition:** Saw bounces at wall, Phil is directly at the bounce point  
**Expected:** Phil takes damage on contact, no desync  
**Verify:**  
- Collision check happens regardless of bounce event  
- Exact boundary alignment doesn't prevent damage  

---

**Test:** SAW-COLL-003  
**Condition:** Two saws on same level, Phil caught between them  
**Expected:** Each saw applies damage independently if Phil overlaps both  
**Verify:**  
- First saw hit: health drops by 1  
- If both saws overlap Phil in same frame: health drops by 2 (1 per saw)  
- Each saw has independent collision  

---

---

## 2. LAVA / SPIKE PITS

### 2.1 Pit Damage
**Test:** PIT-DMG-001  
**Condition:** Phil walks off platform into pit  
**Expected:** Phil takes exactly 2 hearts damage  
**Verify:**  
- `phil.health` decreases by 2  
- Burst particle effect at pit center  
- SFX.hit() plays  

---

**Test:** PIT-DMG-002  
**Condition:** Phil's health is 3, falls into pit (2 damage)  
**Expected:** Phil survives with 1 health (damage goes through, no cap)  
**Verify:**  
- Health = 3 before pit  
- Health = 1 after pit hit  
- Phil is NOT dead  
- Respawn is triggered but philDie() is NOT called  

---

**Test:** PIT-DMG-003  
**Condition:** Phil's health is 2, falls into pit (2 damage)  
**Expected:** Phil dies (health = 0), philDie() flow executes  
**Verify:**  
- Health = 2 before pit  
- Pit damage = 2  
- Health = 0 after pit  
- `phil.dead = true`  
- Death timer starts  
- Lives decrement  
- Respawn timer (1800ms) triggers  

---

**Test:** PIT-DMG-004  
**Condition:** Phil has invincibility from enemy, then falls into pit  
**Expected:** Pit damage applies (pits ignore invincibility like saws)  
**Verify:**  
- Enemy hit Phil → `invTimer > 0`  
- Phil falls into pit  
- Pit damage applies regardless  

---

### 2.2 Pit Respawn Mechanics
**Test:** PIT-RESP-001  
**Condition:** Phil falls into pit while `lastSafeGround` is set to a platform at `{x:400, y:450}`  
**Expected:** Phil teleports back to lastSafeGround, not killed  
**Verify:**  
- Before pit: `phil.x = 250`, `phil.y = 100` (mid-fall)  
- Pit collision: `respawnToSafe` flag triggers  
- After respawn: `phil.x = 400`, `phil.y = 450`  
- `phil.vx = 0`, `phil.vy = 0`  
- Burst particle effect at respawn point  
- Phil is still alive (health > 0)  

---

**Test:** PIT-RESP-002  
**Condition:** Phil falls into pit, `lastSafeGround` is null  
**Expected:** Respawn uses checkpoint or level start  
**Verify:**  
- If `checkpointRespawn` exists: Phil spawns at checkpoint  
- Otherwise: Phil spawns at `levelData.playerStart`  
- Fallback chain: `lastSafeGround` → `checkpointRespawn` → `playerStart`  

---

**Test:** PIT-RESP-003  
**Condition:** Multiple pit hits in sequence (pit respawn + enemy + pit again)  
**Expected:** Each pit respawn works independently  
**Verify:**  
- First pit: respawn at platform A  
- Enemy hits Phil during respawn  
- Phil walks off platform into second pit  
- Second pit: respawn at (updated) lastSafeGround  

---

### 2.3 lastSafeGround Tracking
**Test:** PIT-SAFE-001  
**Condition:** Phil walks on platform, stands still  
**Expected:** `lastSafeGround` updates every frame while conditions are met  
**Verify:**  
- `phil.onGround = true`  
- Standing on real platform (not inside pit)  
- `phil.dead = false`  
- Not overlapping damaging hazard  
- `lastSafeGround = {x: phil.x, y: phil.y}` every frame  

---

**Test:** PIT-SAFE-002  
**Condition:** Phil walks on platform at `x:100`, then jumps to platform at `x:300`  
**Expected:** `lastSafeGround` updates to reflect new platform position  
**Verify:**  
- On first platform: `lastSafeGround = {x:100, y:450}`  
- Jump and land on second platform  
- `lastSafeGround = {x:300, y:450}`  

---

**Test:** PIT-SAFE-003  
**Condition:** Phil is falling over a pit (not on ground)  
**Expected:** `lastSafeGround` does NOT update  
**Verify:**  
- `phil.onGround = false` (mid-fall)  
- `lastSafeGround` remains at last platform position  
- If pit respawn triggers, Phil goes back to that position  

---

**Test:** PIT-SAFE-004  
**Condition:** Phil stands on edge of pit, partially over pit, partially on platform  
**Expected:** `lastSafeGround` does NOT update while overlapping pit  
**Verify:**  
- Pit detection confirms Phil overlaps pit geometry  
- `lastSafeGround` is NOT updated this frame  
- Old `lastSafeGround` is preserved  

---

**Test:** PIT-SAFE-005  
**Condition:** Phil takes damage from enemy while on platform, then immediately falls into pit  
**Expected:** `lastSafeGround` was recorded BEFORE pit fall  
**Verify:**  
- Before enemy hit: Phil on platform, `lastSafeGround = {x:100, y:450}`  
- Enemy hits → `invTimer > 0`  
- Phil gets pushed off platform by knockback  
- Phil falls into pit  
- Pit respawn places Phil at `lastSafeGround = {x:100, y:450}`  

---

### 2.4 Pit Placement & Detection
**Test:** PIT-PLACE-001  
**Condition:** Pit is generated in level, positioned at ground level  
**Expected:** Pit's `y` coordinate is at GROUND_Y, width spans a gap  
**Verify:**  
- Pit `y = GROUND_Y` (same as ground platforms)  
- Pit `h = 20` (visual depth band)  
- Pit `x` and `w` span a gap between ground platforms  
- Pit does not overlap player start zone (first ~350px)  

---

**Test:** PIT-PLACE-002  
**Condition:** Pit overlaps with goal flag position  
**Expected:** Pit is NOT placed at flag or checkpoint  
**Verify:**  
- Pit `x + w` does not include `flagX`  
- Pit does not overlap checkpoint area  

---

**Test:** PIT-PLACE-003  
**Condition:** Pit style is randomized per world (sand, lava, spikes)  
**Expected:** Style matches world rules and renders correctly  
**Verify:**  
- World 2: style is one of `'spikes'`, `'lava'`, `'sand'`  
- Visual appearance matches style  
- All styles have same collision behavior (2 damage)  

---

---

## 3. FALLING ROCKS

### 3.1 Rock Warning Phase
**Test:** ROCK-WARN-001  
**Condition:** Phil walks directly beneath a rock  
**Expected:** Rock enters warning state, dust effect appears, visual wobble  
**Verify:**  
- Rock `state = 'idle'` initially  
- Phil enters `triggerX` range beneath rock  
- Rock state changes to `'warning'`  
- `warningTimer = 0.45` (or configured value)  
- `dustPuff()` is called at rock position  
- Wobble animation starts  

---

**Test:** ROCK-WARN-002  
**Condition:** Phil passes under rock from left to right  
**Expected:** Trigger zone check respects phil.x range  
**Verify:**  
- Rock `triggerX` defines a horizontal band  
- If `phil.x` enters band and Phil is above rock: trigger  
- Trigger fires only once per idle cycle  

---

**Test:** ROCK-WARN-003  
**Condition:** Warning timer counts down from 0.45s to 0  
**Expected:** Rock transitions to falling state after timer expires  
**Verify:**  
- `warningTimer = 0.45` at trigger  
- Every frame: `warningTimer -= dt`  
- When `warningTimer <= 0`: rock state = `'falling'`  
- `vy` is initialized for gravity  

---

### 3.2 Rock Falling & Damage
**Test:** ROCK-FALL-001  
**Condition:** Rock is in falling state, Phil is directly below  
**Expected:** Rock applies 1 heart damage on contact  
**Verify:**  
- Rock `state = 'falling'`  
- Rock bbox overlaps Phil bbox  
- Damage = 1  
- `phil.health` decreases by 1  

---

**Test:** ROCK-FALL-002  
**Condition:** Rock falls and reaches ground or platform  
**Expected:** Rock shatters (burst effect), enters spent state  
**Verify:**  
- Rock `vy` > 0 (moving down)  
- Rock `y + h` >= ground level OR rock collides with platform  
- `burst()` called with shatter color `'#b9976a'`  
- Rock `state = 'spent'`  
- Rock no longer causes damage or moves  

---

**Test:** ROCK-FALL-003  
**Condition:** Rock has fallen and is in spent state  
**Expected:** Rock does not trigger again in same level  
**Verify:**  
- Rock `state = 'spent'` persists  
- Phil can walk under spent rock without retriggering  
- Rock remains in scene for visual fade or is removed immediately  

---

**Test:** ROCK-FALL-004  
**Condition:** Phil has invincibility from enemy, rock falls on him  
**Expected:** Rock damage applies (not blocked by invincibility)  
**Verify:**  
- Enemy hit Phil → `invTimer > 0`  
- Rock falls and hits Phil  
- Damage applies: health decreases by 1  
- Rock still shatters normally  

---

### 3.3 Multiple Rocks in Sequence
**Test:** ROCK-SEQ-001  
**Condition:** Two rocks in same lane, Phil walks through  
**Expected:** Each rock triggers independently  
**Verify:**  
- Rock A warning phase, Phil moves on  
- Rock A falls and shatters  
- Rock B triggered as Phil continues  
- Rock B warning phase starts  
- Both rocks complete life cycle independently  

---

**Test:** ROCK-SEQ-002  
**Condition:** Rock A is falling, Rock B is idle, Phil caught between  
**Expected:** Only falling rock deals damage, idle rock does not  
**Verify:**  
- Rock A state = `'falling'`, overlaps Phil  
- Rock B state = `'idle'`, no overlap (or over platform)  
- Damage = 1 (from Rock A only)  
- Rock B remains idle  

---

**Test:** ROCK-SEQ-003  
**Condition:** Three rocks in rapid sequence on single platform  
**Expected:** Performance remains stable, all rocks behave correctly  
**Verify:**  
- All rocks spawn, update, and collide without frame drops  
- No memory leaks from repeated rock state changes  
- Hazard array size stays manageable  

---

### 3.4 Rock Placement
**Test:** ROCK-PLACE-001  
**Condition:** Rock is generated above traversed lane  
**Expected:** Rock is NOT in safe spawn zone (first ~350px)  
**Verify:**  
- Rock `x > 350` (outside player spawn zone)  
- Rock `x < levelData.flagX` (not at goal)  
- Rock is positioned above a platform or common path  

---

**Test:** ROCK-PLACE-002  
**Condition:** Rock spawn density in World 2  
**Expected:** Rocks introduced at level >= 4, not before  
**Verify:**  
- World 2, Level 1-3: no rocks generated  
- World 2, Level 4+: rocks may be present  
- Density is reasonable (~2-6 rocks per level)  

---

---

## 4. INTEGRATION & EDGE CASES

### 4.1 Hazard + Enemy Interaction
**Test:** INT-ENEMY-001  
**Condition:** Phil is damaged by enemy and saw in same frame  
**Expected:** Both damage sources apply independently  
**Verify:**  
- Enemy hit Phil: health -= 1, invTimer = 1.1s  
- Saw hit Phil in same frame: health -= 1 (sees sees overlap)  
- Total health -= 2 this frame  
- `invTimer` is reset to 1.1s (most recent source)  

---

**Test:** INT-ENEMY-002  
**Condition:** Pit respawn happens while enemies are nearby  
**Expected:** Phil respawns, enemies continue normal behavior  
**Verify:**  
- Pit hit triggers respawn to lastSafeGround  
- Enemies on nearby platforms remain active  
- No collision re-trigger with respawn point  
- No infinite respawn loop  

---

**Test:** INT-ENEMY-003  
**Condition:** Enemy walks through saw or pit  
**Expected:** Enemies are unaffected by hazards  
**Verify:**  
- Saw does not damage enemies  
- Pit does not kill enemies  
- Hazard collision is Phil-only  

---

### 4.2 Shield Interaction
**Test:** INT-SHIELD-001  
**Condition:** Phil is shielding, walks into saw  
**Expected:** Saw damage behavior is unchanged  
**Verify:**  
- Phil shielding: `phil.shielding = true`  
- Saw ignores shields (saws ignore invincibility)  
- Damage applies: health -= 1  

---

**Test:** INT-SHIELD-002  
**Condition:** Phil shields during pit fall  
**Expected:** Shield does not prevent pit respawn  
**Verify:**  
- Phil shielding: `phil.shielding = true`  
- Pit respawn triggers  
- Phil teleported to lastSafeGround  
- Shield state is reset or preserved based on design  

---

### 4.3 Death & Respawn Flow
**Test:** INT-DEATH-001  
**Condition:** Phil's health = 1, falls into pit (2 damage)  
**Expected:** Phil dies, respawnPhil() called after death timer  
**Verify:**  
- Health = 1 before pit  
- Pit deals 2 damage → health = -1 (clamped to 0)  
- `phil.dead = true`  
- `deathTimer = 1.8s` starts  
- After 1.8s: `respawnPhil()` called  
- Phil re-enters level at checkpoint or start  

---

**Test:** INT-DEATH-002  
**Condition:** Phil takes saw damage, gets knocked back into pit  
**Expected:** Both damage sources apply, respawn works  
**Verify:**  
- Saw: health -= 1  
- Knockback sends Phil toward pit  
- Pit: health -= 2  
- If health <= 0: philDie() triggers  
- Otherwise: respawn to lastSafeGround  

---

**Test:** INT-DEATH-003  
**Condition:** Checkpoint respawn after pit death  
**Expected:** Phil respawns at checkpoint, lastSafeGround is reset  
**Verify:**  
- Checkpoint activated at checkpoint X  
- Phil falls into pit far ahead  
- Pit respawn uses checkpoint  
- `lastSafeGround` is reset to checkpoint or prior position  

---

### 4.4 Hazard Placement Rules
**Test:** INT-PLACE-001  
**Condition:** Saws are placed on long safe platforms in World 1  
**Expected:** Saws appear only in later levels per gate  
**Verify:**  
- World 1, Level 1-6: no saws  
- World 1, Level 7+: saws may appear  
- Saws are placed on platforms, not in sky  

---

**Test:** INT-PLACE-002  
**Condition:** Pits are placed at ground gaps in World 2  
**Expected:** Pits introduced early, progress as designed  
**Verify:**  
- World 2, Level 1: no pits  
- World 2, Level 2+: pits may appear  
- Pits match ground-level gaps (not floating)  

---

**Test:** INT-PLACE-003  
**Condition:** Boss arenas contain NO procedural hazards  
**Expected:** Boss levels are hazard-free  
**Verify:**  
- Boss levels (level 5, 10): `hazards` array is empty  
- genBossArena returns no hazard data  
- Boss arena is clean for combat focus  

---

**Test:** INT-PLACE-004  
**Condition:** Hazard density is constrained per level  
**Expected:** No excessive hazard spawning  
**Verify:**  
- Total hazard count <= 8 per level (reasonable upper bound)  
- At most 1 hazard feature per ground chunk  
- Performance remains smooth (>30 fps)  

---

### 4.5 Checkpoint Integration
**Test:** INT-CHECK-001  
**Condition:** Checkpoint is activated mid-level  
**Expected:** Pit respawn to checkpoint works  
**Verify:**  
- `checkpointRespawn` is set when checkpoint touched  
- Pit respawn uses `checkpointRespawn` if set  
- Phil spawns at checkpoint, not at level start  

---

**Test:** INT-CHECK-002  
**Condition:** Pit respawn, then checkpoint respawn  
**Expected:** Each respawn uses correct source  
**Verify:**  
- First pit respawn: uses lastSafeGround  
- Checkpoint touched  
- Second pit respawn: uses checkpoint if lastSafeGround is stale  

---

### 4.6 Camera & Viewport
**Test:** INT-CAM-001  
**Condition:** Rock falls off-screen or hazard is out of view  
**Expected:** Hazard collision and damage still apply  
**Verify:**  
- Rock falling below camera viewport  
- Collision detection does not depend on viewport  
- Damage still applies if Phil overlaps  

---

---

## 5. PERFORMANCE

### 5.1 Hazard Count
**Test:** PERF-COUNT-001  
**Condition:** Level contains 8 hazards (saw, pits, rocks mix)  
**Expected:** Frame rate stays >= 30 fps  
**Verify:**  
- Monitor frame time during gameplay  
- No significant drops when hazards are active  
- No frame skips during hazard collisions  

---

**Test:** PERF-COUNT-002  
**Condition:** Hazard array is updated every frame  
**Expected:** Update loop completes in time budget  
**Verify:**  
- `updateHazards(dt)` runs in < 5ms (assuming 60 fps)  
- Collision checks are efficient (no N² scaling)  

---

### 5.2 Saw Rotation & Animation
**Test:** PERF-ROT-001  
**Condition:** Saw rotation property increments every frame  
**Expected:** No frame drops due to rotation math  
**Verify:**  
- Saw `rot` increases continuously  
- Canvas rotation transform is GPU-accelerated  
- No frame hiccups during saw spin  

---

### 5.3 Rock Wobble & Particles
**Test:** PERF-WOBBLE-001  
**Condition:** Multiple rocks in warning phase, dust particles spawning  
**Expected:** Particle system doesn't cause stutters  
**Verify:**  
- Dust particles are pooled or limited  
- Particle lifecycle is short (fade out quickly)  
- No memory accumulation from particles  

---

**Test:** PERF-SHATTER-001  
**Condition:** Rock shatters and creates burst particles  
**Expected:** Burst particle effect completes without stalling  
**Verify:**  
- Shatter burst spawns limited particle count  
- Particles fade out within 0.5s  
- No long-lived particles clogging memory  

---

### 5.4 Collision Detection
**Test:** PERF-COLL-001  
**Condition:** Hazard collision checks are performed every frame  
**Expected:** Collision check overhead is < 1ms per hazard  
**Verify:**  
- Overlap function is optimized (AABB, not pixel-perfect)  
- No redundant collision checks  

---

---

## 6. REGRESSION TESTS

### 6.1 Existing Mechanics Unaffected
**Test:** REG-PHIL-001  
**Condition:** Phil's attack hit box and enemy collision remain unchanged  
**Expected:** Attack damage to enemies still works  
**Verify:**  
- Phil attacks enemy: enemy takes damage, is pushed back  
- No hazards interfere with attack mechanics  

---

**Test:** REG-PICKUP-001  
**Condition:** Pickups (coins, health) still spawn and are collected  
**Expected:** Pickups unaffected by hazards  
**Verify:**  
- Pickup collision is independent  
- Hazards do not delete or steal pickups  

---

**Test:** REG-PLAT-001  
**Condition:** Platform collision for Phil remains the same  
**Expected:** Phil movement and gravity unaffected by hazards  
**Verify:**  
- Phil lands on platforms correctly  
- No z-fighting or phase-through with platforms due to hazards  

---

**Test:** REG-BOSS-001  
**Condition:** Boss levels load without hazards  
**Expected:** Boss arenas are clean  
**Verify:**  
- Boss arena renders with no saw, pit, or rock  
- Boss behavior is unchanged  

---

---

## Test Execution Guide

### Test Matrix

Use this matrix to track which tests pass / fail:

| Test ID | Category | Expected | Actual | Status | Notes |
|---------|----------|----------|--------|--------|-------|
| SAW-DMG-001 | Damage | 1 heart | ? | ⬜ | |
| SAW-DMG-002 | Damage | Repeated hits | ? | ⬜ | |
| SAW-DMG-003 | Damage | Bypass invincibility | ? | ⬜ | |
| SAW-MOVE-001 | Movement | Bounce at boundaries | ? | ⬜ | |
| ... | ... | ... | ... | ... | ... |

### Priority Order

**High Priority (Block Release):**
- SAW-DMG-001, SAW-DMG-002, SAW-DMG-003 (saw damage is core mechanic)
- PIT-DMG-001, PIT-DMG-002, PIT-RESP-001 (pit respawn is critical)
- ROCK-WARN-001, ROCK-FALL-001, ROCK-FALL-002 (rock lifecycle)
- INT-PLACE-001, INT-PLACE-003 (hazard placement gates)

**Medium Priority (Before Feature Complete):**
- All SAW-MOVE tests (saw movement visual feedback)
- PIT-SAFE-001 through PIT-SAFE-005 (lastSafeGround tracking)
- ROCK-SEQ-001 (multiple rocks)
- INT-ENEMY-001, INT-ENEMY-002 (hazard-enemy interaction)

**Low Priority (Polish):**
- PIT-PLACE-002, PIT-PLACE-003 (pit visuals)
- PERF-* tests (performance tunning)
- REG-* tests (regression, run after core done)

### Environment Setup

- Ensure `levelData` is populated correctly before tests
- Spawn hazards via `spawnHazards(levelData.hazards)` during `startLevel()`
- Verify `hazards` global array is initialized and cleared between levels

### Debugging Notes

- Use console logging to verify hazard state (position, active, damage value)
- Check `overlap()` function output for collision debugging
- Inspect `phil.health` and `phil.invTimer` before/after damage events
- Monitor `hazards` array size to catch memory leaks

---

## Known Unknowns (Clarify with SpongeBob)

1. What is the exact `warningTimer` value for rock warning phase? (Spec says ~0.45s)
2. Does pit respawn reset `phil.shielding` or preserve it?
3. Should hazards emit sound effects on hit? (Only SFX.hit() specified)
4. Is rock shatter debris visual only, or does it stick around for physics?
5. Does `lastSafeGround` persist after pit respawn, or reset to checkpoint?
6. Can hazards spawn inside platform bounding boxes (phased)?

---

## Test Evidence Template

For each test run, document:

```
Test ID: [ID]
Date: [YYYY-MM-DD]
Status: [PASS | FAIL]
Observed Behavior: [What actually happened]
Expected Behavior: [What should happen]
Steps to Reproduce: [How to trigger the test]
Evidence: [Screenshots, console logs, video clip]
Root Cause (if fail): [Analysis]
Next Steps: [What to fix or investigate]
```

---

## Sign-Off

- [ ] All High Priority tests pass
- [ ] All Medium Priority tests pass
- [ ] No regressions in existing mechanics
- [ ] Performance benchmarks met (>30 fps)
- [ ] Hazard density constraints verified
- [ ] Boss arenas confirmed hazard-free

**Tested By:** ________  
**Date:** ________  
**Notes:** ________
