# Validation

2026-10-17 (rollback to single-file 調式生成器 version): Chromium via local HTTP server.

- Page loads with no JavaScript console errors; embedded demo `<audio>` element present with data-URI MP3 source.
- Voice-count slider redraws the matrix (5 → 7 → 5 tracks); mode change updates the scale readout immediately (e.g. Dorian: C · D · E♭ · F · G · A · B♭).
- 生成新旋律 produces a new pattern; 播放 starts Web Audio playback (status 正在演奏, step highlight advances, build stage increments); 停止 halts playback.
- MIDI export code path unchanged from the original single-file version (SMF format 1, 480 PPQ, tempo track + per-voice tracks).

Not re-verified on a physical iPhone. Audio requires a user tap to start; desktop checks do not replace on-device listening.
