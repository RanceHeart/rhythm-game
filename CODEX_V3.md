# V3 — UI Bug Fixes + Real Assets

## Mission
Fix UI bugs in the rhythm game at `/tmp/rhythm-game/index.html`. Stop inventing from scratch — FIND real existing music and reference real rhythm game UIs.

## Critical Principle: 抄 (copy/adapt), don't invent
The user's explicit rule: "硬搓肯定弄不出来好的 我们的宗旨一般是抄". 
- Do NOT invent your own music patterns — find real Creative Commons / royalty-free music online
- Do NOT invent your own UI patterns — look at real rhythm games (osu!, Cytus, Voez, Spin Rhythm, Geometry Dash) on Github/CodePen and adapt their approaches

## Step 1: Fix Known UI Bugs

### Bug 1: 成就横栏 (Milestone Bar) 突然跳出
The `#milestoneBar` at combo milestones is jerky and appears suddenly. Fix:
- Make it feel smooth and weighty (anticipation: start subtle, accelerate, overshoot, settle)
- Real reference: osu! combo burst or Geometry Dash level complete animation
- Should feel like a reward, not a glitch

### Bug 2: Other UI issues to find and fix
Play the game locally and find any other UI glitches:
- Text overlap? Positioning off?
- Animation glitches (things popping in, clipping, wrong timing)?
- Score/combo display weirdness?
- Results screen layout issues?

## Step 2: Replace Procedural Music With Real Audio

### Find free music
Search for Creative Commons / royalty-free music with a clear beat (120-140 BPM). Good sources:
- Free Music Archive (freemusicarchive.org)
- Incompetech (incompetech.com) - Kevin MacLeod
- Pixabay Music
- Or: use an open-source MIDI-to-audio approach

### How to integrate
- Load as an `<audio>` element or via Web Audio API
- Generate beat maps (note timings) programmatically from the music's BPM
- The game needs at least 3 songs to choose from

### Not acceptable
Do NOT keep the procedural Web Audio API drums as the main music. That's what "音乐太少" was about. If you can't find real audio files, find an open-source music generation library instead.

## Step 3: Reference Real UI
Before writing any CSS/JS changes:
1. Search GitHub/gists/CodePen for "rhythm game UI html css"
2. Look at how real games handle:
   - Combo display (position, animation, size hierarchy)
   - Hit feedback (where text appears, how long it stays)
   - Score board layout
   - Settings panel
   - Progress indicator
3. Adapt their patterns, don't invent from scratch

## Step 4: Commit and Deploy
```bash
git add -A && git commit -m "feat: V3 — UI bug fixes, real music assets" && git push
```

After pushing, verify the GitHub Pages URL loads correctly.
