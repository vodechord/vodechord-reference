# Mr Voice VTM — Release notes since 2.2.0

## 2.6.0 — 2026-10-09 · Prosody & automation lanes
**Phrase editor**
- New automation lane under the phoneme strip (60 px). Switch it in the strip header: **F0 · BREATH · SHAPE · —**.
  - **F0**: piano-roll pitch bar per phoneme, semitone grid, note names on the axis. Drag ↕ = semitone, ⇧ = fine, double-click = reset, **↺ F0** resets all. The grey line is the glide that will play; after a take a white line shows what actually played, including held MIDI notes.
  - **BREATH**: lung-pressure envelope and frication, drawn from the same rule the player uses. Read-only.
  - **SHAPE**: simulated tongue height, jaw and lip opening over time.
- Strip glyphs: rests show as `|` and `‖`; stressed and vibrato vowels get small marks; tense vowels show in bold.

**Phrase markup** (typed into the Phrase field)
- `,` short rest · `; —` medium · `. ! ?` end of phrase · `…` long rest. They stack (`,,`).
- `?` rising end · `.` falling end · `!` raised word.
- `*word*` stress · `^word` / `_word` ±2 semitones (stackable) · `word~` vibrato and held vowel · `CAPS` tense voice. Acronyms in caps (e.g. NASA) are also read as tense.
- Speech declination now resets after each rest. Sing mode changes notes once per syllable, not per phoneme.

**Live control**
- A held MIDI note now overrides the phrase F0. It glides from the current F0 in about 35 ms (about 25 ms between notes). On release it glides back to the programmed contour in about 70 ms and the phrase continues.
- LUNG (knob or CC) now takes effect while a phrase is playing.
- Removed the 45 ms start delay. A phrase starts immediately from its first phoneme's shape, and pitch snaps to the first note.

**Transport**
- The SPEAK/SING toggle shows only ◌ / ♪. The play button reads **SAY IT** or **SING IT** to match.
- The toggle is spaced away from the rate buttons; SAY IT is right-aligned with the phrase field.

## 2.5.0 · Fresh starts & proportional transitions
- Every take starts fresh: the tract snaps to the first phoneme's shape with no breath, instead of gliding over from where the last take ended.
- Transition time now scales with each phoneme's duration, so stretching a phoneme slows its glide. Fractions by type: closure 50%, fricative 35%, vowel 45%, liquid/glide 55–60%, diphthong half 80%; every move is kept between 10 and 320 ms.
- During a phrase the engine's own geometry smoothing drops from about 75 ms to about 8 ms, so phrase timing is no longer limited by that fixed lag. Live knob and MIDI playing keeps the slow smoothing.
- Each phoneme block shows a transition ramp; while dragging, the tooltip shows both values (`180ms · ↗81ms`).

## 2.4.x · Phrase pads, library & layout
- **Phrase pads** replace the example chips: 4×1 / 4×2 / 4×4 / 8×4 grids, with pad 1 at bottom-left. Click a pad to play it; click an empty pad to assign the current phrase. **ASSIGN** then a pad replaces it; right-click clears.
- **MIDI PADS** toggle with a selectable start note (default C2 / 36). Pads take priority over the phoneme keys and are one-shot.
- **Phrase library**: LOAD dropdown plus SAVE and DEL. The library and pad assignments are kept in localStorage.
- **Enter** in the Phrase field plays the phrase. Choosing a phrase from the dropdown puts the cursor back in the field.
- **Layout**: the phoneme strip has its own full-width row under the dev tuning bar. The phrase column runs field → library row → transport, with the pads to the right. ASSIGN is on the left of the pad bar, grid sizes on the right.
- Removed the decorative "SCALE 1:1.4" and "FIG. A · MIDLINE TRACT" labels from the diagram.

## 2.3.x · Pronunciation & intelligibility tooling
- **CMUdict lookup**: about 5,000 of the most common English words plus contractions (`cmudict-5k.js`, about 116 KB, public domain), converted to IPA. Lookup order: CMUdict → hand-tuned WORD_DICT → **NRL-76 letter-to-sound rules** for unknown words (`g2p.js`) → old spelling rules.
- **Intelligibility Bench** (separate page, `Intelligibility Bench.html`), with tabs for Guide, Targets, Source, Transitions and Confusion:
  - isolated phonemes; vowel F1/F2 measured from the audio and from the tract shape, against published reference values; a target editor;
  - blind A/B of the physical source against an ideal pulse/noise source;
  - CV / VC / VCV grid, a trouble-pair list, and per-class transition timing;
  - confusion matrix from listener answers or Whisper transcription;
  - a variables panel for changing one variable at a time;
  - an inspector with waveform, spectrogram, tract areas and state logged every 5 ms, exportable as CSV/JSON;
  - a Guide tab with a walkthrough, a plain-English key to all 44 sounds (example word, a tip for making it, ▶ WORD / ▶ VTM) and a link to the full IPA chart.
- Engine (bench-only, off in the live app): scripted tract-shape playback, state logging, ideal-source mode, a random seed, and a `tractSmooth` tuning setting.
- Pressure and voicing rules moved from `app.jsx` to `voice-physics.js`, shared by the app and the bench.

## Known gaps
- Push 2 pad lights are not wired up for the phrase-pad grid.
- The Intelligibility Bench still uses the 2.3 playback logic: no proportional transitions and no markup prosody.
