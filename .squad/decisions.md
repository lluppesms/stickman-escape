# Squad Decisions

## Active Decisions

### Environmental Hazards Architecture (2026-04-28)
**Prepared by:** Squidward  

One-pipeline hazard system: generation defs → runtime objects → update → collision/damage → draw.

**Data Model:**
- Level defs: `{ type, x, y, w, h, ...typeSpecific }`
- Runtime hazard shape: `{ type, x, y, w, h, active, damage, ignoresInvincibility, respawnOnHit, ...typeSpecific }`

**Hazard Types:**
1. **Saw:** Moving blade on x/y axis, width:28 height:28, damage:1, ignoresInvincibility:true, has axis/min/max/speed/dir/rot/track
2. **Pit:** Static ground span, width varies, height:20, damage:2, respawnOnHit:true, style:spikes|lava|sand, pulse animation
3. **Rock:** Falling block, width:26 height:26, damage:1, state-machine (idle→warning→falling→spent), triggers on player proximity, wobbles and shatters

**Placement Rules:**
- World 1 (Forest): saws only, starting level 7+ (no early hazards)
- World 2 (Desert): pits from level 2+, rocks from level 4+
- Boss arenas: no procedural hazards
- Max ~1 hazard per lane chunk; ~2-4 hazards in W1 late, ~4-6 in W2
- Avoid spawn zone (~350px), checkpoint zone, end flag zone

**Update Loop (PLAYING state order):**
1. updatePhil
2. updateEnemies
3. updateBoss
4. updateProjs
5. updatePickups
6. updateHazards
7. updateCam
8. updateParts

**Collision/Damage Refactor:**
```js
philTakeDmg(amt, opts={ignoreInv, setInv, knockback, respawnToSafe})
```
- Saw: `ignoreInv:true, setInv:false` (no i-frames, ignores invincibility)
- Pit: `ignoreInv:true, setInv:false, respawnToSafe:true` (teleports to lastSafeGround)
- Rock: `ignoreInv:false, setInv:true` (normal collision)

**Pit Respawn Tracking:**
- Add `lastSafeGround` global (seed from levelData.playerStart)
- Update only when: Phil on ground + on real platform + not dead + not overlapping hazard
- On pit hit: apply 2 damage → teleport to lastSafeGround/checkpointRespawn/playerStart if still alive

**Rendering Layers:**
- drawHazardsBack(): saw tracks, pit fills, rock anchors
- drawHazardsFront(): saw blades, spike tops, falling rocks
- Hazards embedded in terrain, moving pieces read in front

**Work Split:**
- SpongeBob (logic): genLevel hazard placement, spawnHazards, updateHazards, lastSafeGround tracking, philTakeDmg refactor, collision helpers, startLevel/respawnPhil wiring
- Sandy (rendering): drawHazardsBack/Front, saw/pit/rock art, animations, final draw-order polish

## Governance

- All meaningful changes require team consensus
- Document architectural decisions here
- Keep history focused on work, decisions focused on direction
