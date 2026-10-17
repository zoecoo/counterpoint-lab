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

2026-10-18 (unison merging): Chromium via local HTTP server.

- Overlapping same-pitch notes across voices now merge at the scheduler: a note fully covered by an already-sounding unison is skipped, and a longer one continues seamlessly from the earlier note's end instead of re-attacking. Reproduced the reported case (len-2 note in one voice, same pitch len-1 in another, same start): one bar of playback created 2 oscillators (one tone) instead of 4. No console errors.

2026-10-18 (ghost preview of unrevealed notes): Chromium via local HTTP server.

- In build mode, clicking an apparently empty cell could reveal a hidden note of arbitrary length (quarter/eighth/sixteenth), which felt random. Unrevealed notes now render as faint ghost bars (opacity .18) so the length is visible before clicking; clicking a ghost reveals it in place, and truly empty cells still create a single sixteenth. Ghosts disappear when 逐步加入 is off. No console errors.

2026-10-18 (auditory-streaming candidates, 16-voice slider): Chromium via local HTTP server.

- Resultant analysis rewritten around auditory stream segregation: three streams anchored at the 85th/50th/15th pitch percentiles accept only notes within ±6 semitones of a slowly-moving focus (rests where the band is empty), plus an accent line from multi-voice simultaneous attacks. Verified candidate spans of 7–9 semitones (previously extreme lines spanned several octaves); scoring now weighs interval smoothness, register compactness, and density.
- 起始聲部數 slider max raised to 16; 16 canon voices render in the matrix and play within the existing caps. No console errors.

2026-10-18 (conventional ties): Chromium via local HTTP server.

- Notes crossing a beat boundary are now split at the boundary and tied (ABC "-"), per engraving convention; previously each note was emitted as a single duration so ties never appeared (lengths cap at a quarter and never cross the barline). Verified a len-2 note starting at step 3 renders tied, and generated textures show ties throughout; visual check via SVG→PNG. No console errors.

2026-10-18 (two-column layout): Chromium via local HTTP server.

- At ≥1000px the scale/mode and playback panels sit in a 350px sticky right column (scrollable if taller than the viewport) with matrix/score/composer on the left (864px content at 1280px viewport, no horizontal overflow); below 1000px the layout stacks in the original single-column order. All control ids remain reachable; no console errors.

2026-10-18 (audition toggle): Chromium via local HTTP server.

- The candidate audition button now toggles: it reads ■ 停止試聽 while playing and clicking it again stops; the label resets on natural end, on the main stop button, and moves when switching to another candidate's audition. No console errors.

2026-10-18 (Reich-style variation + audition button isolation): Chromium via local HTTP server.

- 變化 panel with a master 啟用 checkbox (default off = strict canon; verified all voices identical when off and stable through playback). When on: complementary subsets at generation (verified differing per-voice note counts with union coverage of the motif), per-bar micro-variation (verified cell changes during playback with live matrix/score refresh), and every-N-bars subset reconfiguration (verified with N=2).
- During audition the main play button no longer flips to ■ 停止 (the audition card has its own toggle); clicking the main play button mid-audition switches straight into full playback and resets the audition button. No console errors.

2026-10-18 (saner tie-splitting): Chromium via local HTTP server.

- Splitting every beat crossing turned ordinary syncopations (e.g. a quarter starting at step 2) into dotted-note-plus-tie clutter with long flat arcs across the multi-voice spacing. Notes now split only at the half-bar: verified a step-2 quarter renders as a single quarter, a step-7 eighth splits into tied sixteenths across the midpoint, and a dotted eighth at 13–15 stays unsplit (ABC totals still 16). Both dense and sparse textures re-validated as well-formed ABC. No console errors.

2026-10-18 (material-voice button): Chromium via local HTTP server.

- ＋ 新增素材聲部 appends a voice built from the motif at the next canon shift index (verified 素材 1 at shift 10 with the same note count as canon voices, complementary subset applied when 變化 is on); the original button is renamed ＋ 新增自訂聲部 and still adds an empty voice. Both survive canon rebuilds. No console errors.

2026-10-18 (pitch input): Chromium via local HTTP server.

- An 輸入音高 selector (22 scale degrees, G2–A6 under pentatonic major, tonic default) sets the pitch used when clicking an empty cell; verified a selected A4 lands in a custom voice (range label A4–A4), and mode changes relabel the options while preserving the selected degree. No console errors.

2026-10-18 (editable motif row + overlap-safe mutations): Chromium via local HTTP server.

- A 母題 row above the voices edits the shared source melody with the same click model and pitch selector; verified removing/adding motif notes immediately rebuilds all canon voices in sync (note counts follow the motif) while the staggered-entry dimming skips the motif row.
- Micro-variation now rejects revive/move targets covered by another note's sustain and clips revived/moved lengths to the free space; a 9-second 50%-probability mutation fuzz at 180 BPM produced zero orphan bar fragments (previously overlapping notes drew broken slivers).
