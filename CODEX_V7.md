# V7 — Gameplay Feel: Hit Sound + Readable Flow

## The Problem
Game feels hollow. Tap has no audio reward. Notes fly in on random curving paths — hard to read. Feedback is all visual, no tactile/audio satisfaction.

## Changes (3 things, nothing else)

### 1. Hit Sound via Web Audio
Add a short synthesized "click/ping" on every tap. No audio files needed.
- On PERFECT: 880Hz sine, 80ms, gentle release (feels like a "ding")
- On GOOD: 660Hz sine, 60ms (slightly lower, shorter)
- On OK: 440Hz sine, 45ms (duller)
- On MISS: 180Hz sine, 100ms, with a slight noise burst (feels bad — motivates player to not miss)
- Volume: 25% of music volume setting (audible but not overwhelming)
- Create a tiny function `playHitSound(tier)` using OscillatorNode + GainNode

```javascript
function playHitSound(tier) {
  if (!audioCtx) return;
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  const freqs = { perfect: 880, good: 660, ok: 440, miss: 180 };
  const durs = { perfect: 0.08, good: 0.06, ok: 0.045, miss: 0.1 };
  osc.type = 'sine';
  osc.frequency.value = freqs[tier] || 440;
  gain.gain.value = 0.12 * (volume || 0.5);
  if (tier === 'miss') {
    // add a second noise-like oscillator for miss
    osc.type = 'sawtooth';
    osc.frequency.value = 120;
  }
  gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + durs[tier]);
  osc.connect(gain).connect(audioCtx.destination);
  osc.start(audioCtx.currentTime);
  osc.stop(audioCtx.currentTime + durs[tier]);
}
```

Call it in `judgeHit()` after tier is determined, and in `registerMiss()`.

### 2. Remove hitRing element + animation
- Delete the `.hit-ring` CSS block
- Remove `<div id="hitRing"...>` from the HTML
- Delete the `animate('#hitRing', {...})` call in hitFeedback
- The localized effect is gone. Hit feedback = judge label + reduced particles + target ring bounce only.

### 3. Clean note approach — straight lines, readable
Currently `makeCurve()` creates random quadratic SVG curves. This is confusing — you can't tell where a note is going.

Replace with **straight-line** approaches from 4 cardinal directions. Each note flies in a straight line toward center:
- direction cycles: top→bottom→left→right→top...
- No SVG path needed — use CSS transform: translate() with anime.js instead of getPointAtLength()
- This eliminates `flightSvg`, `makeCurve()`, and all SVG path creation per note
- Use anime.js timeline for each note: translate from edge position to center over TRAVEL duration
- Note "echo" trail: 2 faded clones behind it at fixed offsets, same straight line

This is how osu! and most rhythm games work — predictable, readable, learnable.

Implementation:
- Delete `flightSvg` (the SVG layer), `makeCurve()`, `noteEdge()`
- Replace with `getNoteStart(index)` returning {x, y} based on cycling direction
- Replace `updateNotePosition()` — instead of per-frame path traversal, use anime.js `animate()` with `translateX`/`translateY` from start to center (500, 281)
- Each note gets an anime.js animation that moves it from edge → center over TRAVEL seconds
- On miss (auto-miss when time runs out), cancel the note's animation

But wait — this is a BIG refactor of the note system. The current approach uses requestAnimationFrame + `updateNotePosition()` per frame to move notes along paths. Changing to anime.js per-note animations would conflict with the game loop.

Let me think of a simpler approach: Keep the per-frame RAFL loop for note position, but make the path straight instead of curved.

Simpler approach:
- Replace `makeCurve()` with a straight-line path (just `M startX,startY L 500,281`)
- Replace `noteEdge()` with a simpler cycling 4-direction start position
- Keep `updateNotePosition()` and `getPointAtLength()` — everything works the same, just straight lines now
- This is a 5-line change

Actually the simplest change: make the curve bend amount 0 or minimal. Currently `makeCurve` has `const bend = ((index % 5) - 2) * 44;` which gives bends of -88 to 88. Just make bend = 0 or very small.

Even better: remove the Q curve entirely and just use an L (straight line):

```javascript
function makeCurve(start) {
  const path = document.createElementNS('http://www.w3.org/2000/svg', 'path');
  path.setAttribute('d', `M${start.x},${start.y} L500,281`);
  path.setAttribute('fill', 'none'); path.setAttribute('stroke', 'none');
  flightSvg.appendChild(path);
  return path;
}
```

This is the smallest change for biggest readability improvement.

Also cycle noteEdge more predictably — use a simple counter instead of pseudorandom:

```javascript
function noteEdge(index) {
  const side = index % 4; // 0=top, 1=right, 2=bottom, 3=left
  const p = 120 + ((index * 59) % 360); // varied position along edge
  if (side === 0) return { x: p, y: -30 };
  if (side === 1) return { x: 1035, y: p };
  if (side === 2) return { x: p, y: 597 };
  return { x: -30, y: p };
}
```

### 4. Target ring "anticipation pulse" — critical for readability
When a note is within 300ms of arrival, make the target ring pulse faster and brighter. This gives the player a clear visual cue that they need to tap NOW.

In the gameLoop, find the nearest un-judged note. If it's within 0.3s of target time:
- Set targetRing border-color to the note's color (briefly)
- The ring should grow slightly more prominent

Implementation: in `gameLoop()`, after processing notes:
```javascript
// Anticipation cue — nearest note glows the target ring
const nearest = notes.filter(n => !n.judged).reduce((a, b) => 
  Math.abs(b.targetTime - now) < Math.abs(a.targetTime - now) ? b : a, null);
if (nearest && Math.abs(nearest.targetTime - now) < 0.3) {
  $('#targetRing').style.borderColor = nearest.el?.style?.color || 'var(--accent-2)';
  $('#targetRing').style.boxShadow = '0 0 25px currentColor, inset 0 0 25px currentColor';
} else {
  $('#targetRing').style.borderColor = '';
  $('#targetRing').style.boxShadow = '';
}
```

### Summary of files to change
All in `/tmp/rhythm-game-v7/index.html`:
1. Add `playHitSound(tier)` function
2. Call it in `judgeHit()` and `registerMiss()`
3. Delete `.hit-ring` CSS + HTML element
4. Delete `animate('#hitRing',...)` in hitFeedback
5. Replace `makeCurve()` — straight line L path, no Q bend
6. Replace `noteEdge()` — simple `index % 4` cycling
7. Add anticipation pulse in gameLoop

### What NOT to change
- Keep the reduced particles from V6 (8/6/4, no SVG paths)
- Keep no splitText on score pop
- Keep no screen shake
- Keep no full-screen flash
- Keep all other gameplay mechanics intact
