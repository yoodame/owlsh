# owlsh

A terminal-style sight words game for early readers. Built as a single HTML file — no backend, no build step, no dependencies.

**Play it now:** [yoodame.github.io/owlsh](https://yoodame.github.io/owlsh/)

## What is this?

Sight words are high-frequency words that kids learn to recognize on sight without sounding them out. They make up roughly 50-75% of all text in early reading. **owlsh** turns learning these words into a fast, fun terminal game.

## Features

- **220 Dolch sight words** across 5 skill levels (Hatchling → Owl Master)
- **5 game modes:**
  - **Flash** — word appears briefly, type it from memory
  - **Fill** — complete the missing letters
  - **Scramble** — unscramble jumbled letters
  - **Rush** — 60-second speed challenge
  - **Story** — fill in the blank in a sentence
- **Skill Discovery** — short assessment that places you at the right level
- **Adaptive difficulty** — missed words come back more often (spaced repetition)
- **Multiple profiles** — siblings can share one device
- **Progress tracking** — per-word mastery with a 5-star system
- **Sound effects** — audio feedback via Web Audio API
- **Works offline** after first load

## Tech

| What | How |
|------|-----|
| UI | Single `index.html`, ~1400 lines |
| Styling | Tailwind CSS (CDN) |
| Logic | Vanilla JavaScript |
| Data | localStorage (no backend) |
| Auth | None needed — local profiles only |
| Deploy | GitHub Pages (static) |

## Run locally

Just open the file:

```
open index.html
```

Or serve it:

```
python3 -m http.server 8080
# then visit http://localhost:8080
```

## How it works

All game state lives in `localStorage` under the key `owlsh_data`. Each profile tracks:

- Skill level (discovered via assessment or set manually)
- Per-word mastery: times seen, times correct, current streak, star rating (0-5)
- Stats: total score, sessions played, best streak, words mastered

Words are selected adaptively — lower mastery words appear more frequently. A word is "mastered" after getting it right 3 times in a row across sessions.

## Keyboard controls

| Key | Action |
|-----|--------|
| `Enter` | Submit answer / advance |
| `ESC` | Go back / exit current game |
| `1-5` | Menu selections |
| `0` | Back |

## Credits

Built at [Common Sense Media](https://www.commonsensemedia.org/) exploring AI-assisted development with [Claude Code](https://claude.ai/claude-code).
