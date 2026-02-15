# NeonBlox - Tetris PWA

## Project Overview
NeonBlox is a fully offline-capable Tetris Progressive Web App built for iPhone. The game features neon arcade aesthetics, touch controls, synthesized sounds, particle effects, and personalized messages from Ozan.

**Live URL**: https://ozan2025.github.io/NeonBlox/

## Architecture

### Single-File Design
- `index.html` - Complete game (~70KB) with all CSS/JS inline
- `manifest.json` - PWA manifest with inline SVG icons
- `sw.js` - Service worker with cache-first strategy

### Key Technologies
- **Vanilla JavaScript** - No frameworks, pure JS game engine
- **Canvas API** - Dual canvas (game board + particle effects)
- **Web Audio API** - All sounds synthesized (no audio files)
- **Service Worker** - Offline-first PWA with localStorage persistence
- **Google Fonts** - Orbitron font (cached by SW)

## Game Features

### Core Mechanics
- **Standard Tetris Rules**: 10×20 grid, 7 tetromino types
- **SRS Rotation System**: Super Rotation System with wall kicks
- **Lock Delay**: 500ms with 15-move reset limit
- **7-Bag Randomizer**: Fair piece distribution
- **Scoring**: 100/300/500/800 × level for 1/2/3/4 lines
- **Speed Progression**: Increases every 10 lines cleared

### Controls
- **Touch**: Tap=rotate, swipe L/R=move, swipe down=soft drop, swipe up=hard drop
- **Keyboard**: Arrows, Space (hard drop), Z (rotate CCW), P/Esc (pause)
- **On-screen buttons**: Backup touch controls at bottom

### Visual Effects
- **Neon glow** on all blocks with color-coded pieces
- **Ghost piece** showing drop destination
- **Particle explosions** on line clears
- **Screen shake** on multi-line clears (bigger on Tetris)
- **Combo text popups** (DOUBLE!, TRIPLE!, TETRIS!)
- **Speed-based board glow** (changes hue with level)

### Audio
All sounds generated via Web Audio API oscillators:
- Move click (800Hz square)
- Rotate chirp (200→600Hz sweep)
- Land thud (150→50Hz triangle)
- Line clear sweep (300→1200Hz sawtooth)
- Tetris fanfare (4-note arpeggio)
- Hard drop slam (white noise + 80Hz sine)
- Game over descend (6-note sequence)

### Personalization
- **Welcome messages**: Random greeting from Ozan on game start
- **Tetris messages**: Celebration on 4-line clears ("Ozan is proud! 🎉")
- **Milestone messages**: 10K, 20K, 50K score achievements
- **Production credit**: "AN OZAN SELCUK PRODUCTION" on start screen
- **Version number**: Displayed on start screen (currently v1.1)

## Development

### Version Management
**IMPORTANT**: When making updates, always bump the version number in TWO places:

1. **Service Worker Cache** (`sw.js`):
   ```javascript
   const CACHE_NAME = 'neonblox-v1.X';
   ```

2. **Start Screen** (`index.html`):
   ```html
   <p class="version">v1.X</p>
   ```

This forces cache refresh on deployed updates.

### Local Development
```bash
# Serve locally
python3 -m http.server 8001

# View at http://localhost:8001
```

### Deployment
```bash
# Push to GitHub (auto-deploys to GitHub Pages)
git add .
git commit -m "Description"
git push

# Live at https://ozan2025.github.io/NeonBlox/
# Updates appear in 1-2 minutes
```

### Testing on iPhone
1. Visit https://ozan2025.github.io/NeonBlox/ in Safari
2. Tap Share → "Add to Home Screen"
3. Game works fully offline after first load
4. To update: Close app completely, reopen (fetches new version in background)
5. Second open loads new version

For immediate update: Delete from home screen and re-add from Safari

## Code Architecture

### Canvas Strategy
- **game-canvas**: Static board + pieces (z-index: 1)
- **fx-canvas**: Particles + text popups (z-index: 2)
- **next-canvas**: Preview of next piece

### Responsive Sizing
```javascript
CELL = min(availW/14, availH/20)
// 14 = 10 board cols + 4 preview cols
// Clamped between 14px-32px
```

### Game State Machine
```
START → (tap) → PLAYING → (pause/P) → PAUSED
                   ↓
              (game over) → GAMEOVER → (tap) → PLAYING
```

### Tetromino Data
- Pre-computed 4×4 rotation matrices for all 7 pieces
- 4 rotation states each (0, R, 2, L)
- SRS wall kick tables with y-values negated (y-down coordinate system)

### Performance
- **Target**: 60fps on iPhone 12+
- **Game loop**: requestAnimationFrame with delta-time (capped at 100ms)
- **Particle system**: ~400 max particles, simple array with splice-on-death
- **No allocations** in hot paths (game loop, render)

## File Locations
- **Source**: `/Users/ozanselcuk/2026_Developer/PWA-Tetris/`
- **GitHub**: https://github.com/ozan2025/NeonBlox
- **Live**: https://ozan2025.github.io/NeonBlox/

## Known Constraints
- Google Font (Orbitron) requires internet on first load
- Service worker only works over HTTPS (or localhost)
- iOS requires specific meta tags for proper PWA behavior
- Line clear animation delays row collapse by 350ms

## Future Enhancement Ideas
- Difficulty modes (faster/slower)
- Different color schemes
- Hold piece feature
- Combo multipliers
- Achievement system
- Sound effects toggle per-type
