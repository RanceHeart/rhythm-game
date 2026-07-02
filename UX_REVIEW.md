# Rhythm Game V2 — UX Review

## Step 1 — Test notes

The required `python3 -m http.server 8080` command was attempted twice (default bind and
`127.0.0.1`) but this environment rejects socket creation with `PermissionError:
[Errno 1] Operation not permitted`. Chrome DevTools automation was also unavailable
because its debugging endpoint was not running. The review therefore used the complete
source, the existing gameplay screenshot, and static/runtime validation. Browser replay
must be repeated in an environment that permits a localhost server.

- The primary start control is clickable, but settings are only exposed through a small
  passive scroll cue.
- The 30-second track repeats essentially the same one-bar percussion idea.
- The song carousel advertises two locked songs, which makes the interface look unfinished.
- The custom volume drag starts from a CSS offset that the drag model does not know about;
  grabbing it can make the value jump.
- The hit windows are readable and internally consistent, but an empty input is immediately
  scored as a miss without explaining why.
- Feedback is visually strong, but the score pop does not include the active multiplier.
- Progress is a small ring with no time remaining.
- The desktop HUD is readable. Portrait mode keeps a 16:9 game surface, shrinking the
  playable area into a horizontal strip.
- Results have a breakdown, but “retry” is styled text rather than a real control and the
  detail page depends on an easy-to-miss scroll instruction.
- Touch input exists, but controls and layout do not clearly communicate mobile support.

## Step 2 — User rant

“Why does the only song end just as it starts getting interesting? It is the same tiny
drum loop over and over, and the other song cards literally say locked. The settings are
hidden below the fold, the volume jumps when I grab it, and the carousel does not actually
let me choose anything useful. On a phone the whole game is crushed into a skinny strip.
The progress ring tells me almost nothing, and after the song I have to notice tiny text,
scroll, and then press a keyboard key because retry is not even a button. Stop making me
decode the interface and let me play.”

## Step 3 — PM triage

### P0 — must fix

- Replace the short repeating track with a substantially longer, sectioned arrangement.
- Keep chart timing aligned with the extended music.
- Fix portrait/mobile gameplay sizing.
- Replace the jumping volume drag with a reliable accessible control.

### P1 — should fix

- Turn the fake locked carousel into three selectable procedural mixes.
- Make settings and results details reachable through explicit buttons.
- Make retry a real button and prevent unrelated keys from accidentally restarting.
- Add time information to progress and clarify empty-tap feedback.
- Improve result readability with grade context and clear accuracy labeling.

### P2 — polish

- Differentiate song sections with harmony, instrumentation, palette, and chart density.
- Respect reduced-motion preferences more completely.
- Improve focus states, hover states, touch targets, and compact-height layouts.
- Show the actual multiplied score in hit feedback.
