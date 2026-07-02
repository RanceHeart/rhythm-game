# Rhythm Game — Complete Build Spec

## Mission
Build a **geometric rhythm game** using **anime.js v4** that delivers maximum visual impact, tension, and kinetic energy. Single HTML file. No frameworks. Published on GitHub Pages at `/` (root). This IS the repo's homepage.

## Core Game Mechanic
- A circle/square target sits at center screen
- **Notes (geometric shapes) fly in from all directions** toward the center target
- Player presses **SPACE / F / J** (configurable, default SPACE) in sync with the beat when a note reaches the target
- **Perfect** hit → screen bursts, particles, combo multiplier
- **Early/Late** hit → dim feedback, lower score
- **Miss** → screen desaturation, combo break animation
- **Built-in demo song** generated via Web Audio API (procedural beat, no external audio file needed)

## Visual Style — Reference Games

### Primary: BIT.TRIP RUNNER / BEAT (Choice Provisions)
- **Pure geometric shapes** — circles, squares, diamonds, triangles, lines
- **Neon on deep black** (not pure black, like #0a0a0f)
- **Synesthetic visuals** — every beat changes the environment
- **Retro 8-bit meets modern neon** — sharp pixels, blooming glows
- **Camera pulse** — the entire screen scales/rotates subtly on each beat

### Secondary: Voez / Cytus II (Rayark)
- **Beat-reactive particles** that burst from hit points
- **Graceful overshoot on everything** — no easing "in/out" without overshoot
- **Screen-wide light bursts** on combo milestones
- **Motion blur** via directional scale stretching on fast movements

### Tertiary: Spin Rhythm XD
- **Color-coded notes** (red/blue/green/yellow) each with distinct shape
- **Clean UI** — score, combo, accuracy all minimal and integrated
- **Hit zone glow ring** that pulses with the beat

## Anime.js v4 Feature Checklist — USE ALL OF THESE

The entire point is to showcase what anime.js can do. Every feature below MUST be used in a visible, non-trivial way:

### 1. `createTimeline()` — Orchestrated sequences
- Intro sequence: shapes fly in, form target, beat starts → all via timeline
- Combo milestones: choreographed multi-shape celebrations
- Game over sequence: slow-motion collapse of the interface

### 2. `stagger()` — Cascading delays
- Note spawn animation: notes stagger in from edges
- Particle bursts: particles stagger outward from hit point
- UI elements: score digits stagger on combo change

### 3. `spring()` Physics — Bouncy, overshooting motion
- NOTE: anime.js v4 spring uses `stiffness` + `damping` (NOT `mass`):
  ```js
  animate(target, {
    scale: [0, 1],
    ease: 'spring(stiffness: 200, damping: 10)'
  })
  ```
- Target circle: constant subtle spring on idle (breathing effect)
- Score pop: spring on every score increment
- Combo counter: spring-reset to normal on milestone

### 4. Elastic Easing — `ease: 'outElastic(1, 0.5)'`
- Note hit confirmation pulse
- Target ring expansion on hit
- Combo multiplier badge wobble

### 5. SVG Morph — `morphTo()`
- Notes transition between geometric shapes (circle → square → triangle) based on combo level
- Background geometric frame morphs between scenes
- Logo / title morph animation

### 6. Motion Path — `motionPath()`
- Notes fly along curved paths, not just straight lines
- Particles follow randomized bezier curves on burst
- Decorative elements orbit along circular paths in background

### 7. `createDraggable()` — Interactive
- Volume slider (draggable) in settings
- Song selection carousel (draggable horizontally)

### 8. `onScroll()` — Scroll-triggered
- Start screen: scroll down to reveal instructions / settings
- Results screen: scroll to see detailed stats breakdown

### 9. `waapi.animate()` — Performance-critical
- Background gridlines pulsing with beat (use WAAPI for 60fps on many elements)
- Particle systems for sustained effects
- Screen shake effect (micro-transforms on root container)

### 10. `splitText()` — Text animation
- Title screen: "Click to Start" splits and cascades in
- Score pop: "PERFECT!" / "MISS" splits and animates letter by letter
- Results: "COMBO x50" splits with stagger

### 11. SVG Utilities (`createDrawable`, SVG filters)
- Glow/bloom effect on hit shapes (SVG `<filter>` with `feGaussianBlur`)
- Draw progress ring: `draw: ['0 0', '0 1', '1 1']` on song progress circle

## Motion Principles (CRITICAL — DO NOT SKIP)

Codex produces stiff animations by default. You must override this.

### 1. Anticipation Before Action
- Before a note is judged: the target ring **contracts slightly** (to 0.95x), then **bursts open** on hit (to 1.3x overshoot)
- Before a combo milestone: all UI elements **scale down 0.98x** for 100ms, then **burst**
- Before a new section: screen **flash/hold black** for 1 frame, then reveal

### 2. Overshoot on EVERY Landing
Every animation MUST overshoot its target before settling:
- Hit circle: scale goes 0 → 1.3 → settle to 1.0
- Particles: velocity starts fast, slows with overshoot at end
- UI panel: slides in going 110% of distance, then bounces back to 100%

### 3. Overlapping Action
- When a note hits, 3 things happen with staggered timing:
  1. Frame 0-6: Hit burst (scale explosion of target)
  2. Frame 3-15: Particles spawn from hit point
  3. Frame 10-30: Background pulse (camera zoom 1.0→1.05→1.0)
- The tail of one animation overlaps the start of the next

### 4. Screen Shake on Impact
- On PERFECT hit: micro-shake (2-3px amplitude, 100ms, decays exponentially)
- On combo milestone: medium shake (5px, 150ms)
- On MISS: vertical shake + slight red flash

### 5. Beat-Synced Everything
- Background grid lines pulse every beat (scale 1.0→1.02→1.0)
- Color scheme shifts subtly every 4 beats
- On strong beat (every 4th): more intense visual event

## Game Architecture

### File Structure (single HTML file — everything inline)
```
rhythm-game/
  index.html          ← THE GAME. This is it. Pages root serves this.
  README.md           ← Already exists. Update with game info + screenshot.
```

### Technical Requirements
- **Single HTML file** — all CSS in `<style>`, all JS in `<script>`
- **anime.js v4** loaded from CDN: `https://cdn.jsdelivr.net/npm/animejs@4.0.0/lib/anime.esm.js` (use import map or script module)
- **Web Audio API** — procedural beats (no external mp3 needed). Generate kick/snare/hi-hat patterns via OscillatorNode + GainNode
- **No other dependencies** — pure anime.js + Web Audio API + vanilla JS
- **Responsive** — works on 1920x1080 down to 1280x720 (16:9 game area centered)
- **60fps target** — use WAAPI for particle/background systems that need many elements, reserve anime.js for hero animations
- **Keyboard only** — SPACE main, F/J for left/right if implementing multi-lane (optional, default single-lane SPACE)
- **Mobile touch** — tap anywhere acts as SPACE press

### Game States
1. **TITLE** — Logo animates in (anime.js timeline), "Press SPACE to Start" pulses with elastic easing
2. **PLAYING** — Notes fly in, player hits, combo counter, score, song progress bar
3. **RESULTS** — Score breakdown, rank (S/A/B/C/D), "Press SPACE to Retry" — all animated with anime.js stagger

### Scoring System
- PERFECT: ±25ms window → 300pts + combo multiplier
- GOOD: ±75ms window → 200pts
- OK: ±150ms window → 100pts
- MISS: beyond 150ms → 0pts, combo breaks
- Combo multiplier: x1 at 5, x2 at 15, x3 at 30, x4 at 50
- Final rank: S (>95%), A (>85%), B (>70%), C (>50%), D (<50%)

### Demo Song
Generate procedurally via Web Audio API:
- BPM: 128 (adjustable)
- Pattern: 16-beat loop (4 bars of 4/4)
- Kick: on 1, 5, 9, 13 (all quarter notes, but accent 1 & 9)
- Snare: on 3, 7, 11, 15 (backbeat)
- Hi-hat: every 8th note (1e&a 2e&a...)
- Bass: simple sine wave pattern following kick
- Notes are pre-mapped to the beat grid so the game is always playable

## Visual Design Spec

### Color Palette
```css
--bg: #0a0a0f;           /* Deep near-black */
--bg-secondary: #12121a;  /* Slightly lighter for panels */
--accent-1: #ff3366;      /* Hot pink — primary hits */
--accent-2: #33ffcc;      /* Cyan — combo milestones */
--accent-3: #ffcc33;      /* Yellow — PERFECT feedback */
--accent-4: #6633ff;      /* Purple — menu/highlight */
--text: #ffffff;
--text-dim: rgba(255,255,255,0.4);
--glow-1: rgba(255,51,102,0.3);  /* For box-shadow / SVG glow */
--glow-2: rgba(51,255,204,0.3);
```

### Typography
- Use system sans-serif stack OR one Google Font (e.g. "Jersey 15" or "Press Start 2P" for retro feel via @import)
- Game UI (score, combo): bold, large, tracking looser

### Layout
```
┌─────────────────────────────────────────┐
│  SCORE: 00000     COMBO: x0     ♫ ─●── │
│                                           │
│                                           │
│          ┌─────────────────────┐          │
│          │   ◯ TARGET ZONE    │          │
│          │         hit        │          │
│          │    ┌───┐          │          │
│          │    │ ◉ │          │          │
│          │    └───┘          │          │
│          │       notes fly⟶  │          │
│          └─────────────────────┘          │
│                                           │
│  ━━━━ PERFECT! ━━━━                       │
└─────────────────────────────────────────┘
```

### Hit Feedback Layers (at the same time, overlapping)
1. Target ring: scale 0.95→1.3→1.0 (anticipate→burst→settle) via spring()
2. Hit flash: radial gradient burst from center, 150ms, ease: 'outCubic'
3. Particles: 20-40 geometric dots (circle/diamond/square) burst outward with stagger + motionPath
4. Score pop: score number splits up with splitText(), drifts upward 50px, fades
5. Label: "PERFECT"/"GOOD" appears, scales in with elastic, fades after 600ms
6. Screen shake: root element transformed 2-3px, decays
7. Background pulse: entire game container scale 1.0→1.02→1.0 over 2 beats
8. Combo milestone (every 5): screen-wide flash bar top to bottom

## Implementation Order (Codex: build in this sequence)

### Phase 1: Foundation
1. HTML structure with all zones (title/play/results)
2. CSS: color palette, typography, layout, glow effects, responsive sizing
3. anime.js ESM import via CDN
4. Web Audio API: procedural beat generator (128 BPM, kick/snare/hi-hat)
5. Beat clock: start audio → emit beat events on 16th-note grid

### Phase 2: Core Game Loop
6. Note spawner: generates notes from randomized edge positions, paths to center
7. Note flight: notes fly along curved paths toward target using anime.js motionPath()
8. Hit detection: compare note arrival time vs player input time (±timing windows)
9. Target zone: pulsing ring with anticipation animation
10. Score + combo system with all scoring tiers

### Phase 3: Visual Impact System
11. Hit feedback (all 8 layers listed above) — THIS IS THE MOST IMPORTANT PHASE
12. Particle system using stagger + motionPath
13. Screen shake effect
14. Beat-reactive background (grid pulses, color shifts)
15. Combo milestone celebrations (screen flash, shape morphs)

### Phase 4: Polish
16. Title screen with anime.js timeline intro
17. Results screen with stagger + splitText
18. Settings overlay (volume, key binding)
19. Song progress bar (SVG draw animation)
20. Mobile touch support

### Phase 5: Deploy
21. Update README.md with game info, instructions, screenshot
22. Make sure index.html is at repo root
23. Verify GitHub Pages serves it
24. Test in browser — game must be fully playable end-to-end

## Critical Rules for Codex

1. **You are building this in /tmp/rhythm-game/ directory.** Write all files there.
2. **index.html is the ONLY file players see.** Everything inline. Anime.js via CDN. Web Audio API for music. No external dependencies.
3. **Test your build before saying done** — run `python3 -m http.server 8080` from the directory and verify it loads without console errors.
4. **DO NOT produce stiff animations.** Every single animation must have anticipation + overshoot. This is the #1 most important requirement.
5. **The game must be PLAYABLE.** A player should be able to open the page, press SPACE, and play through a full song with scoring.
6. **After everything works**, update README.md with: project title, description, how to play (controls + scoring), links to anime.js, and a note that this was built with anime.js v4.
7. **Push all changes to git:**
```bash
git add -A && git commit -m "feat: complete rhythm game with anime.js v4" && git push
```
8. **Verify the Pages deployment** — wait 30s, then curl or open the github.io URL to confirm it loads.
