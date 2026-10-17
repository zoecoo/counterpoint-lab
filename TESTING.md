# Validation

2026-10-17 (feature port onto single-file 調式生成器 version): Chromium via local HTTP server, desktop and 375×812 mobile.

- Page loads with no JavaScript console errors on desktop and mobile; no document horizontal overflow at 375px.
- Cell click toggles note/rest; per-voice 靜音 dims the row and 恢復 restores it; ✕ removes a voice.
- 分析候選音型 finds 4 distinct candidates with scores; 加入新聲部 appends 衍生 voices and clears stale candidates; ✦ 自動選出並加入 adds the top-scored line; audition plays one pass and stops.
- Changing 聲部數 rebuilds canon voices while keeping 衍生 voices.
- 播放 schedules Web Audio with derived voices included (step highlight advances); 停止 and candidate audition both halt cleanly.
- Exported MIDI re-validated after fixing a header bug inherited from the original single-file version (MThd length bytes were doubled): MThd length 6, format 1, 480 PPQ, correct track count, and MTrk chunk sizes consume the file exactly.

Not re-verified on a physical iPhone. Audio requires a user tap to start; the page stops playback when hidden.

2026-10-17 (toggle button, note lengths, notation): Chromium via local HTTP server.

- Single play/stop toggle (▶ 播放 ↔ ■ 停止, red tint while playing); standalone 停止 button removed.
- Generated patterns mix 1/2/4-step note lengths; matrix shows sustains as translucent cells; clicking cycles 休止 → 1 → 2 → 4 → 休止, and edited cells become immediately visible in build mode.
- Score panel renders per-voice SVG staves (clef by average pitch, accidentals, ledger lines, flags by duration) and updates on edits; scrolls inside its own container with no page overflow at 375px.
- MIDI re-validated (MThd length 6, format 1, 480 PPQ, exact chunk sizes); durations clipped to the next note start per voice.
- No JavaScript console errors.
