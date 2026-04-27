# Phil's Escape

A browser-based platformer game built with vanilla HTML5 Canvas and JavaScript — no dependencies, no build step, just open `index.html` and play.

Phil is a warrior from the Stickman Army who's trying to break free. Fight through two worlds packed with enemies, mini-bosses, and world bosses to earn your freedom.

---

## Play

**Play online:** https://natethedrummer.github.io/stickman-escape/

Or open `index.html` in any modern browser (Chrome, Firefox, Safari, Edge).

---

## Controls

| Action | Keys |
|--------|------|
| Move | `A` / `D` or Arrow Keys |
| Jump | `Space`, `W`, or `↑` |
| Sword Attack | `Z` or `J` |
| Shield (hold) | `X`, `K`, or `Shift` |
| Restart | `R` or `Enter` |

Mobile touch controls appear automatically on touch devices.

> **Shield tip:** The shield only blocks arrows coming from the front — face the archer before raising it.

---

## Game Structure

### Worlds

| World | Theme | Levels | Mini Boss (Lvl 5) | World Boss (Lvl 10) |
|-------|-------|--------|-------------------|----------------------|
| 1 | Forest | 10 | Stickman Giant | Green Anaconda |
| 2 | Desert | 10 | Stickman Giant | Giant Robot Lizard |
| — | Final | — | — | Giant Stickman Mech |

### Flags
- 🔵 **Blue flag** — Checkpoint (saves your respawn point, awards +100 score)
- 🔴 **Red flag** — Level finish line

### Enemies
- **Stickman Soldier** — Melee, patrols and chases Phil
- **Stickman Archer** — Ranged, fires arrows that can be blocked with the shield
- **Heavy Soldier** — Slow, shielded brute with 4 HP; introduced in World 2 level 3+
- **Bomb Thrower** — Lobs destroyable bombs and retreats at close range; introduced in World 2 level 5+
- **Stickman Glider** — Fast flying diver that must be hit with a jump attack; introduced in World 1 level 7+

### Scoring
| Event | Points |
|-------|--------|
| Kill soldier | +100 |
| Kill archer | +150 |
| Kill heavy soldier | +200 |
| Kill bomb thrower | +175 |
| Kill glider | +125 |
| Checkpoint | +100 |
| Level complete | +500 |
| Boss kills | +800–3000 |

---

## Features

- Procedurally generated levels (seeded per world + level number — consistent every run)
- 5-heart health system with half-heart damage
- Sword + shield dual combat mechanic
- Expanded enemy roster with shielded heavies, bomb lobbers, and diving gliders
- 4 unique boss fights with multi-phase AI
- Parallax scrolling backgrounds
- Web Audio API synthesized sound effects (no audio files)
- Particle system for hits, dust, and deaths
- Mobile touch controls
- High score saved to localStorage

---

## Roadmap

The current game is a complete v1.0 experience. Here's what's planned:

### Core improvements
- **Checkpoint respawn** — dying respawns Phil at the last blue flag instead of ending the run
- **Save progress** — remember which world/level you've reached between sessions
- **Difficulty modes** — Easy / Normal / Hard with tuned enemy speed, damage, and boss health
- **Pause menu** — Escape key to pause mid-level, with resume / restart / quit options

### More content
- **New worlds** — additional themes beyond Forest and Desert
- **Enemy variants & elite encounters** — tougher remixes beyond the standard roster
- **Environmental hazards** — moving saws, lava pits, falling rocks
- **Moving platforms** — horizontal movers, elevators, falling platforms
- **Collectible power-ups** — health refills, invincibility stars, speed boosts, score multipliers
- **Kill combo system** — chain kills quickly for a score multiplier

### Polish & progression
- **World map & level select** — visual overworld with locked/unlocked level nodes
- **Unlockable character skins** — cosmetic rewards for completing worlds and achievements
- **Story cutscenes** — canvas-drawn comic panels at key moments (intro, boss fights, ending)
- **Settings screen** — volume controls and fullscreen toggle

### Creator tools
- **Level maker** — in-browser editor to build and test custom levels
- **Share levels via URL** — encode custom levels into a shareable link (no backend needed)

### Online
- **GitHub Pages deployment** — play in the browser without downloading anything

All open issues and discussion live in the [GitHub Issues](https://github.com/natethedrummer/stickman-escape/issues) tab.

---

## Version History

See [CHANGELOG.md](CHANGELOG.md).

---

## Tech

- Pure HTML5 Canvas + vanilla JavaScript
- No frameworks, no dependencies, no build tools
- Single file (`index.html`) — everything is self-contained
