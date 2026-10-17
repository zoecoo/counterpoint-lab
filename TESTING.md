# Validation

2026-10-17: Playwright Chromium and WebKit at 390×844.

- Analyze, audition, manually add, automatically add, toggle matrix, regenerate, play, stop, download MIDI: passed with no JavaScript page errors.
- Web Audio context running, oscillator scheduling observed in both engines.
- No document horizontal overflow; matrix scrolls independently.
- Downloaded MIDI parsed: format 1, six tracks, expected BPM, valid chunk boundaries, balanced note-on / note-off events.
- Mobile screenshot visually reviewed.

Desktop WebKit is a compatibility check, not a physical iPhone listening test. iOS requires a direct user gesture to resume audio; playback calls resume inside button interaction and stops when the document becomes hidden.
