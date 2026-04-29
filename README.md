# 🥷 Phil's Escape

> *One stickman. Six worlds. Zero mercy.*

[![Play Now](https://img.shields.io/badge/▶%20Play%20Now-Online-brightgreen?style=for-the-badge)](https://natethedrummer.github.io/stickman-escape/)
[![No Dependencies](https://img.shields.io/badge/dependencies-zero-blue?style=for-the-badge)](index.html)
[![Single File](https://img.shields.io/badge/single--file-index.html-orange?style=for-the-badge)](index.html)

---

![Phil's Escape – Title Screen](https://github.com/user-attachments/assets/b278f32e-e827-4bf1-9f42-7fcc83ac44c4)

---

**Phil** is a warrior from the Stickman Army who's tired of taking orders. 🗡️ Slash enemies, 🛡️ block arrows, 💣 dodge bombs, and fight your way through **six brutal worlds** — Forest, Desert, Snow Peaks, Factory 999, the Mines, and Berlin — before facing the ultimate **Giant Stickman Mech** in a final showdown. Can you earn your freedom? 🏃

- 🌲 **6 themed worlds** with 10 levels each
- 👹 **13 boss fights** — mini-bosses AND world bosses
- ⚔️ **Sword + shield** dual combat — timing is everything
- 💀 **5 enemy types**, from archers to bomb throwers to diving gliders
- 📱 **Works everywhere** — desktop keyboard or mobile touch controls
- 🔊 **Synthesized audio** — no sound files, all generated in-browser
- 🏆 **High score** saved locally so you can beat yourself

No installs. No accounts. No nonsense. Just open the page and play. 🎮

---

## 🎮 Play

**Play online:** https://natethedrummer.github.io/stickman-escape/

Or download and open `index.html` in any modern browser (Chrome, Firefox, Safari, Edge).

---

## 🕹️ Controls

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

## 🗺️ Game Structure

### Worlds

| World | Theme | Levels | Mini Boss (Lvl 5) | World Boss (Lvl 10) | Hazards |
|-------|-------|--------|-------------------|----------------------|---------|
| 1 | Forest | 10 | Stickman Giant | Green Anaconda | Moving Saws (level 7+) |
| 2 | Desert | 10 | Stickman Giant | Giant Robot Lizard | Lava Pits (level 2+), Falling Rocks (level 4+) |
| 3 | Snow Peaks | 10 | Frost Warden | Yeti | — |
| 4 | Factory 999 | 10 | Foreman-9 | Bone Revenant | — |
| 5 | Mines | 10 | Tunnel Fiend | Mole Mk.999 | — |
| 6 | Berlin | 10 | Riot Captain | FBI Agent | — |
| — | Final | — | — | Giant Stickman Mech | — |

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

## ✨ Features

- Procedurally generated levels (seeded per world + level number — consistent every run)
- 5-heart health system with half-heart damage
- Sword + shield dual combat mechanic
- Expanded enemy roster with shielded heavies, bomb lobbers, and diving gliders
- 6 themed worlds with 13 boss fights, all using multi-phase AI
- Parallax scrolling backgrounds
- Web Audio API synthesized sound effects (no audio files)
- Particle system for hits, dust, and deaths
- Mobile touch controls
- High score saved to localStorage

---

## 🛣️ Roadmap

The current game is a complete v1.0 experience. Here's what's planned:

### Core improvements
- **Checkpoint respawn** — dying respawns Phil at the last blue flag instead of ending the run
- **Save progress** — remember which world/level you've reached between sessions
- **Difficulty modes** — Easy / Normal / Hard with tuned enemy speed, damage, and boss health
- **Pause menu** — Escape key to pause mid-level, with resume / restart / quit options

### More content
- **More worlds & routes** — harder regions beyond Berlin, with remixed layouts and hazards
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

## 📜 Version History

See [CHANGELOG.md](CHANGELOG.md).

---

## 🔧 Tech

- Pure HTML5 Canvas + vanilla JavaScript
- No frameworks, no dependencies, no build tools
- Single file (`index.html`) — everything is self-contained
