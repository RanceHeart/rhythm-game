# V5 — Visual Design Overhaul (CRITICAL)

## The Problem
Current game scores ~60/100 on visuals. It's flat, average, lacks detail. Changes are too safe, too uniform. Needs depth, density, and drama.

## Reference Games (steal from these)

### 1. osu! — Hit Circle Feedback
- **Approach circle**: a ring that shrinks toward the hit target over time. Gives visual anticipation.
- **Hit burst**: on click, circle explodes outward with a radial ripple + particles. 
- **Combo fire**: combo number pulses with fire-like glow at high combos.
- **Kiai time**: screen-wide glow/particle burst during intense sections.

### 2. Cytus II — Depth & Atmosphere
- **Dynamic scanline overlay**: horizontal scanline that moves up the screen continuously, with slight color shift behind it.
- **Note trail**: every moving note leaves a ghost trail that fades.
- **Bass-reactive background**: background elements pulse in color/scale on low frequencies.
- **Floating particles**: tiny ambient particles (stars/dots) float across screen constantly, responding to beat.

### 3. Geometry Dash — Transitions & Impact
- **Level transition**: brief screen flash (white/color) on restart and milestone.
- **Death burst**: on miss, screen desaturates + shatter particles explosion.
- **Pulse on beat**: EVERY element on screen does a subtle micro-pulse on the beat (not just the target).
- **Trail system**: moving objects leave a fade-out trail behind them.

### 4. ADOFAI (A Dance of Fire and Ice) — Minimal Polish
- **Perfect easing**: every movement has anticipation→action→overshoot→settle. No linear motion anywhere.
- **Color progression**: background subtly shifts hue through the song.

## Concrete Visual Improvements (implement ALL of these)

### 1. Approach Ring (osu!-style)
- When a note spawns, an **approach ring** appears around the TARGET (not the note).
- It starts large (1.5x target size) and **shrinks** toward the target ring over the note's travel time.
- When it reaches the target edge → that's the PERFECT hit moment.
- This gives players a clear VISUAL cue for timing, not just audio.
- Implement as CSS animation: `scale: [1.5 → 1.0]` synchronized to note arrival.

### 2. Note Trail
- Every flying note leaves behind a **fading ghost trail** (2-3 previous positions).
- Use anime.js timeline with delayed opacity decrease on trail elements.
- Color: same as note color, opacity 0.4 → 0, scale slightly larger.

### 3. Hit Burst Rework (osu! radial ripple)
Instead of the current simple particles:
- On PERFECT hit: a **radial ring** expands outward from hit point (like osu! hit burst). Ring starts thin and bright, expands to fill area, fades.
- Then secondary **shockwave ring** follows 100ms later, wider but dimmer.
- Particles shoot out along the ring edge, not just randomly.
- On GOOD hit: smaller ring, fewer particles.
- On OK hit: just a flash, no ring.
- On MISS: screen tint red briefly, target shatters.

### 4. Ambient Particle Layer
- 50-80 tiny floating dots/diamonds in background at all times.
- They drift slowly, changing color with the current palette.
- ON BEAT: they all pulse outward briefly (scale 1.0→1.3→1.0).
- Use WAAPI for performance (50+ elements).

### 5. Scanline Overlay (Cytus II style)
- A horizontal bar (2px tall, full width) that slowly scrolls down the screen.
- Behind it, content has slightly different hue/brightness (use mix-blend-mode or filtered clone).
- Very subtle — barely noticeable consciously, but adds depth.

### 6. Background Evolution
- Background color doesn't stay static.
- Slowly hue-shifts from the song's palette through complementary colors over the song duration.
- Grid lines change opacity and color with the shift.
- Use CSS custom properties and animate them gradually.

### 7. Combo Display Rework
- At 0-4 combo: small, white, minimal.
- At 5-14 combo: cyan glow starts.
- At 15-29 combo: medium size, pulsing with "x2" glow.
- At 30-49 combo: large, animated fire-like glow (use multiple layered text-shadows with different colors).
- At 50+ combo: MAX SIZE, screen-wide glow pulse on each hit, particles burst from combo text.

### 8. Hit Flash Detail
Current hit flash is too simple. Replace with:
- On PERFECT: screen gets a brief **chromatic aberration** flash (red/green channels split by 2-3px for 80ms).
- On combo milestone: full white flash that fades to the accent color.
- The flash should have a **radial gradient** (bright at center, soft at edges), not uniform.

### 9. Score Pop Detail
Current score pop "+300" floats up and fades. Improve:
- Use `splitText()` on the number.
- Each digit pops sequentially with stagger.
- On PERFECT: digits are large, golden, with shadow glow.
- On GOOD: smaller, cyan.
- On OK: small, purple, quick fade.
- Score pop trails a gold dust particle.

### 10. Title Screen Depth
- Logo should have a subtle floating animation (yoyo: translateY -3px to +3px, slow).
- Background behind logo: slow-rotating geometric mandala or concentric rings.
- "Press SPACE" text: more dramatic entrance — letters drop in from above with elastic bounce, one by one.
- Settings panel should slide in with overlapping stagger: first the label, then the controls.

### 11. Results Screen
- Rank letter should ENTER dramatically: scale from 0 to 1.3 overshoot, with a brief glow burst behind it.
- Stats bars should animate in sequence: Perfect bar first, then Good, then OK, then Miss.
- Final score number should "roll up" like a slot machine (each digit increments rapidly, overshoots final value by a few, then settles).

### 12. Note Design Variation
Not all notes look the same:
- Quarter notes (strong beat): **larger, bright, diamond shape** — more important
- Eighth notes: medium circle, standard color
- Sixteenth notes: **small dot**, dimmer color — less important
- This creates visual HIERARCHY so players can SEE the beat structure.

## Animation Timing Principles (non-negotiable)
1. **Every animation** must have: anticipation → action → overshoot → settle
2. **No linear easing anywhere** — `ease: 'linear'` is banned
3. **Overlapping action**: when something happens, 3-4 things react with staggered delays
4. **Screen shake** on PERFECT but subtle (1-2px, not 5px)
5. **Beat-reactive**: every beat causes a visible micro-event somewhere

## Implementation
1. Edit `/tmp/rhythm-game/index.html`
2. Add ALL the improvements listed above
3. Do NOT break gameplay — test after each major change
4. Commit and push when done

```bash
git add -A && git commit -m "feat: V5 — major visual overhaul, osu/Cytus/ADoFAI-inspired effects" && git push
```
