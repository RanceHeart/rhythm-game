# UX Review & Fix — Rhythm Game V2

## Mission
Do a full UX review of the current game at `/tmp/rhythm-game/index.html`, identify ALL user experience problems, fix them, and verify.

## Step 1: Play the game yourself (REQUIRED)
Run `python3 -m http.server 8080` in the repo directory, then open `http://localhost:8080` in a browser. Actually play through the game:
- Start the game
- Play a full song
- Get to the results screen
- Try settings (volume, key binding)
- Try scroll interactions
- Try the draggable song selection

Take notes on EVERYTHING that feels wrong, confusing, broken, or ugly.

## Step 2: User Rant (pretend you're a real user venting)
Write down complaints as if a user is yelling at you. Be brutally honest. Cover:

### UI/UX Issues to check:
1. **Title screen** — Is "Press SPACE to Start" obvious? Is the start button working? Does the scroll cue work? Does the settings panel scroll into view?
2. **Gameplay** — Are notes visible? Do they fly along smooth paths? Is hit feedback satisfying? Is the audio synced? Does screen shake feel right? Does the combo display work?
3. **Hit detection** — Does PERFECT/GOOD/OK/MISS feel fair? Timing windows correct?
4. **Results screen** — Does it show clear breakdown? Is the scroll to see stats working? Retry button?
5. **Mobile** — Does tap work?
6. **Settings** — Volume draggable? Key binding works?
7. **Music** — Only one song? Is it varied enough? 256 steps of the same 4-bar loop is too short!
8. **Visual polish** — Font choices? Alignment? Glow effects? Color harmony? Scanlines/noise overlay? Ambient dots?
9. **Any console errors** — Check browser dev tools console
10. **Responsive** — Does it look OK at different sizes?

### Specific complaints the user had:
- "UI有些部分不太合理" — Some UI parts are not reasonable
- "音乐太少" — Not enough music / too short

## Step 3: Product Manager Triage
Prioritize issues:
- **P0 (must fix)** — Game-breaking, confusing, or makes the game unplayable
- **P1 (should fix)** — Annoying, looks bad, degrades experience
- **P2 (nice to fix)** — Polish, minor visual tweaks

## Step 4: Fix Everything
Fix all P0 and P1 issues. Fix as many P2 as you can.

### Music fix (CRITICAL):
The user said "音乐太少" — this is about the procedural song being too short (only 256 steps = ~30 seconds). Fix options:
1. **Make the song longer** — Instead of 256 steps (32 beats), make it 512 steps (64 beats) or longer with more varied patterns
2. **Add variation** — The current 16-step pattern repeats identically. Add:
   - A section B with different pattern (e.g. half-time feel, different kick pattern)
   - A bridge section 
   - An intro with just hi-hat before full beat drops
   - An outro with gradual fade
3. **More instruments** — Add a simple melodic component (triangle wave arpeggio?) that changes every section
4. **More notes** — Map notes to the extended song so the gameplay matches the longer track

### UI fixes (CRITICAL):
The user said "UI有些部分不太合理". Common issues to check and fix:
1. **Start confusion** — Should clicking "Press SPACE" button also start? Should there be a clearer CTA?
2. **Feedback clarity** — Is PERFECT/GOOD/OK clearly differentiated? Colors? Size? Duration?
3. **Results readability** — Are stats clear? Can user understand their performance?
4. **Settings panel** — Does it work on first scroll? Is key binding feedback clear?
5. **Progress bar** — Does the SVG draw progress actually work? Is it visible?
6. **Score display** — Big enough? Position makes sense?

## Step 5: Verify
1. Play through again — full game loop
2. No console errors
3. Song feels longer and more varied
4. UI feels polished and reasonable
5. Commit and push

## Step 6: Deploy
```bash
git add -A && git commit -m "feat: UX overhaul — longer varied music, UI polish, bug fixes" && git push
sleep 30
curl -fsSL https://ranceheart.github.io/rhythm-game/ > /dev/null && echo "✅ Deployed"
```
