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

2026-10-17 (notation readability): Chromium via local HTTP server.

- Clef is now chosen per voice to minimize ledger lines (treble, bass, or clef-8 ottava variants); verified a 5-voice render assigning 𝄢 to the low-octave voices and an 8-marked clef to a high voice.
- Row heights are computed from each voice's actual pitch extent; verified zero noteheads crossing into a neighboring voice's region (previously rows overlapped).
- Ledger lines reduced to ~0.7 per note on a representative render and drawn at 60% opacity.

2026-10-17 (abcjs engraving): Chromium via local HTTP server.

- Score panel now rendered by vendored abcjs v6.7.2 from generated ABC notation: real clef/note glyphs, 4/4 time signature, automatic beaming per beat (verified beam elements present), quarter/eighth/sixteenth rests.
- Per-voice clef chosen among treble, bass, treble+8, bass-8 by minimizing ledger lines (ottava clefs write the part an octave shifted); verified treble+8 draws its 8 above the clef.
- Measure-level accidental state machine avoids redundant accidentals; %%stretchlast fills the staff width.
- Voice colors applied via abcjs classes; muted voices gray. Score re-renders on cell edits, mute, and adding derived voices (6 staves after add). No console errors.

2026-10-17 (rhythmic resultant patterns): Chromium via local HTTP server.

- analyze() now extracts accent skeletons instead of a pitch per step: per-step salience = simultaneous attacks + beat weighting; 高音線/低音線 keep local pitch peaks/dips (capped at 9), 連貫線/交錯線 use the 7 most salient steps; note lengths extend to the next selected step (capped at a quarter).
- Verified candidates carry 7–9 notes with rests between (previously 16/16); scoring now weighs continuity, accents, and density around 7 notes.
- Audition, add (derived voice shows sustains and renders as a 6th staff), and full playback all work with no console errors.

2026-10-17 (matrix click responsiveness + phase-aware score): Chromium via local HTTP server.

- Matrix DOM is now rebuilt only on structural changes; cell clicks, beat highlights, and build-phase reveals update classes on cached elements (verified the clicked element survives, handler returns in <1ms). Score re-engraving is debounced 250ms off the click path. touch-action:manipulation and user-select:none added for mobile taps.
- Score now follows the build phase: 10 notes rendered at 建構階段 2 vs 40 with 逐步加入 off, and it grew from 11 to 16 notes during playback as the phase advanced (re-engraved once per phase change, scheduled off the audio loop).
- Mute label toggles, voice removal, candidate analysis, and staff count after removal all verified; no console errors.

2026-10-17 (range labels, unified voice names, in-place mute): Chromium via local HTTP server.

- Matrix voice labels show the voice's range on a second line (e.g. Voice 1 / E3–A4), recomputed on cell edits and root/mode changes.
- Score voice names show the same name + range text, styled via CSS to system-ui 11px gray matching the matrix labels.
- Mute during playback verified: the button toggles in place (no DOM rebuild), the row dims, audio filtering applies per step, and playback continues.
- No console errors.

2026-10-18 (range bars, lookahead sync, uniform clicks, continuous note bars): Chromium via local HTTP server.

- Voice labels show a horizontal range bar (voice min–max positioned within the global pitch span, tooltip with note names); verified distinct positions per octave layer and updates on mode change.
- Playback rescheduled on the AudioContext clock with 120ms lookahead; beat highlight and phase re-engraves fire at the scheduled audio time. Measured step intervals 134–137ms against a 134ms target at 112 BPM (was drifting with setTimeout pacing).
- Uniform cell interaction verified: clicking an attack or sustain cell silences it (mid-bar click truncates), clicking the cell right after a bar re-extends it (cap 4), other empty cells create a note; hidden notes reveal on first click in build mode.
- Notes render as continuous bars (rounded ends, gap-filling connectors at both grid gaps); computed styles verified. No console errors.

2026-10-18 (stacked score voice names): Chromium via local HTTP server.

- Score voice names now render on two lines (name, then range in 9.5px gray) via a post-render tspan, so abcjs reserves margin for the short name only (~37px vs the previous full-width string). Muted marker moves to the range line and clears on unmute. Verified visually via SVG→PNG capture; no console errors.

2026-10-18 (rounded beat ring, add-voice button, staggered entry): Chromium via local HTTP server.

- Beat highlight redrawn as an ::after ring (2px solid, 8px radius) so all four corners are rounded regardless of the cell's own bar-shape radii.
- ＋ 新增聲部 button below the matrix appends an empty editable 自訂 voice (derived-flagged so slider rebuilds keep it); verified immediate cell editing and the 16-voice cap path.
- 每 N 小節加入一個聲部 slider (0=同時) gates voices by bar during playback with waiting rows dimmed: with N=1 and 6 voices, 5 rows waited at start, 3 after two bars, 0 after stop. Loop reset point takes the max of build completion and full entry. No console errors.

2026-10-18 (fix: silent build-up starts): Chromium via local HTTP server.

- Since notes gained lengths, the reveal order was shuffled over all 16 slots, so the first revealed slots could all be rests — every voice then stayed silent (and blank in the matrix) for several bars, which read as "no sound at all", especially with a long voice-entry interval. The reveal order now shuffles note slots first, so the initial build phase always exposes two sounding notes per voice.
- Verified across 10 regenerations: every voice shows exactly 2 note starts at the initial phase; playback produces sound and step highlights immediately. No console errors.
