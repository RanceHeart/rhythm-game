# V4 — Music Length + Beat Sync (CRITICAL FIX)

## The Problem
The game plays real audio files but the note timing is still calculated from a THEORETICAL BPM grid, not synced to the actual audio playback. That's why beats feel off. Also tracks are too short.

## Root Cause
- `songStart` is set when the game starts
- `STEP = 60 / BPM / 4` calculates a theoretical 16th-note duration
- `scheduleAudioStep` and note spawning use `songStart + stepIndex * STEP`
- But the real audio file plays at its own pace — if its actual BPM differs from the assumed 128 BPM, or if it has tempo variations, notes drift off-beat

## Fix: Real Audio Beat Tracking

### Approach: Audio Timeline Sync
Instead of scheduling notes from a theoretical grid, use the actual `<audio>` element's `currentTime` as the source of truth:

1. **Load longer real audio tracks** (90-120 seconds minimum, clear steady beat)
2. **Pre-compute beat timestamps** — create an array of absolute timestamps (in seconds) for every beat/16th-note in the audio file
3. **Hit detection reads `audio.currentTime`** and checks against the nearest beat timestamp
4. **Note spawning:** spawn notes at `currentTime - leadTime` relative to their target beat timestamp

### Implementation plan
1. Generate beat timestamp arrays programmatically based on known BPM:
   ```js
   // Instead of STEP = 60/BPM/4, precompute absolute timestamps
   function generateBeatMap(bpm, totalBeats) {
     const beatDuration = 60 / bpm; // quarter note
     const arr = [];
     for (let i = 0; i < totalBeats * 4; i++) { // 16th notes
       arr.push(i * beatDuration / 4);
     }
     return arr;
   }
   ```
2. Hit detection uses `musicPlayer.currentTime` directly — compare current playhead position to nearest beat timestamp in the map
3. Note animation timing uses `performance.now()` on start, with expected arrival at the beat timestamp translated to real time

### Find Longer CC Music
Search for tracks that are:
- **90-120 seconds long minimum**
- **Clear, steady beat** (electronic/dance genre ideal)
- **Creative Commons / royalty-free**
- Sources: Free Music Archive, Incompetech (Kevin MacLeod), Pixabay Music, ccMixter

### Fix the Volume Slider
The volume slider now controls `<audio>.volume` which is good, but make sure it's working reliably (check that `musicPlayer` is defined before setting volume).

## Deliverable
After fixing:
1. Play the game — audio and notes should be in sync
2. Music should last significantly longer (90s+)
3. Volume slider works
4. Commit and push

```bash
git add -A && git commit -m "feat: V4 — real audio sync, longer tracks" && git push
```
