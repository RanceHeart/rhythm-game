# V6 — Mobile Performance: Fix Hit Effect (Too Bright + Laggy)

## The Problem
On real phones (iPhone/Android), the center hit effect is:
1. **Too bright** — full-screen white radial flash (#hitFlash) at 95% opacity every tap = blinding
2. **Too laggy** — 32 DOM particles per hit + SVG motion paths + splitText DOM nodes + screen shake = frame drops

## The Fix — Reference: osu! Hit Effect Style

Replace the current multi-layer flash with a **localized expanding ring** at the target center. osu! does it right: a colored ring expands from the hit point, no full-screen flash, minimal particles.

### Changes to Make

#### 1. Replace full-screen #hitFlash → Localized hit ring
- Remove the full-screen `.flash` div behavior
- Add a `.hit-ring` element that sits **inside the target-wrap area** (centered at the target)
- On hit: ring scales from 0 to 1.5× target size and fades out
- Ring color matches tier (gold=perfect, cyan=good, purple=ok)
- Ring has a thin stroke (1.5px) and a subtle glow (box-shadow)
- NO full-screen flash — the ring is localized to ~180×180px area

CSS for hit-ring:
```css
.hit-ring {
  position: absolute;
  left: 50%; top: 50%;
  width: 100px; height: 100px;
  margin: -50px;
  border-radius: 50%;
  border: 1.5px solid currentColor;
  opacity: 0;
  pointer-events: none;
  z-index: 9;
  box-shadow: 0 0 8px currentColor;
}
```

#### 2. Reduce particles: 32 → 8 max. No SVG motion paths.
- PERFECT: 8 particles, gold
- GOOD: 6 particles, cyan  
- OK: 4 particles, purple
- Particles fly outward **in straight lines** (not SVG curves) using CSS `translate` animation
- Use anime.js `animate()` with translateX/translateY — no SVG path elements at all
- This eliminates the `flightSvg`<path> creation per particle
- Particle CSS: simpler, just `position:absolute; width:6px; height:6px; border-radius:50%`

#### 3. Simplify score pop — no splitText
- Remove splitText on the score pop number
- Animate the whole `<div>` element — scale up + fade out
- This eliminates creating N extra `.char` DOM nodes per hit
- Same visual effect, zero DOM overhead

#### 4. Remove screen shake + gameShell wobble
- Delete the WAAPI screen shake (`waapi.animate(app, { translateX: ... })`) — it triggers expensive layout
- Delete the gameShell scale wobble (`animate(gameShell, { scale: ... })`) 
- Mobile doesn't need this, it just causes jank

#### 5. Keep: target ring bounce + judge label
- The target ring scale bounce (line 928) is fine — keep it, it's good visual feedback
- The judge label animation (PERFECT!/GOOD/OK) is fine — keep it
- The timing label is fine — keep it

#### 6. Summary of what to DELETE from hitFeedback function:
- `animate('#hitFlash', ...)` line → replace with hit-ring animation
- `burstParticles(32...)` → change to burstParticles(8...) with simpler anim
- splitText on score pop → replace with simple div animation
- WAAPI screen shake → delete entirely
- `animate(gameShell, {...})` → delete entirely

#### 7. CSS cleanup
- Keep `.flash` element in HTML (might be used elsewhere) but the hit effect won't trigger it
- Add `.hit-ring` style
- Simplify `.particle` style (smaller, no diamond/circle variants needed)

## Implementation
1. Edit `/tmp/rhythm-game-v6/index.html`
2. Make ALL changes listed above
3. Test visually in browser after each change
4. Commit and push when done

```bash
git add -A && git commit -m "fix: V6 — tone down hit effect brightness, optimize particles for mobile (osu! style)" && git push
```
