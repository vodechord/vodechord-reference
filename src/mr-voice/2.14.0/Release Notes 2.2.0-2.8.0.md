# Mr Voice VTM — Release notes since 2.2.0

## 2.8.0 — 2026-10-10 · Voice size & rack alignment
- **VOICE SIZE** (Singing Style panel): vocal-tract length from 11 to 20 cm, with presets CHILD 12.5 · F 14.5 · M 16.0 (default) · L 17.5. Double-click resets. The value is remembered.
  - The engine sets tract length by section count, so the voice no longer depends on the device sample rate. At 48k the step is 0.36 cm; the readout shows the actual length.
  - Moving it mid-note resamples the tube geometry and waves, so it glides rather than restarts.
  - Telemetry and bench logs stay in 44-point profile space, so the diagram and bench are unaffected.
- **Rack alignment**: the rack columns now match the main area, so Singing Style / Phoneme Bank sits under Fig A and Source sits under Figs B–D. Source knobs are two rows of three.

## 2.7.0 — 2026-10-10 · Default phrases & canonical rate
**Default phrases.** Eight phrases load onto the pads, and together they demo every markup:
| Pad | Phrase | Shows |
|---|---|---|
| 1 | Hello world. | plain speech, `.` falling end |
| 2 | Are you there? | `?` rising end |
| 3 | Wait, let me think… okay! | `,` short rest · `…` long rest · `!` raised |
| 4 | I *really* mean it. | `*stress*` |
| 5 | Go ^up, then _down. | `^` / `_` pitch steps |
| 6 | Sing it~ with me~. | `~` vibrato |
| 7 | Stop. That is LOUD! | `CAPS` tense voice |
| 8 | Say /h ɛ l oʊ/ — then breathe; done. | `/ipa/` inline · `—` `;` medium rests |

- The pads default to **4×2**. The list also keeps three classics (quick brown fox, sea shells, I can speak).
- **↺** in the library row restores the defaults; your saved phrases stay in the list.
- Saved data moved to a new storage key, so everyone starts with the clean set. Phrases you saved before are kept in the list.

**Sample rate.** Back to a **canonical 48 kHz** by default. Tract length scales with the sample rate (48k ≈ 16 cm, 44.1k ≈ 17.5 cm, about 8% lower formants), and all tuning is done at 48k. The panel still offers 44.1k and DEVICE for A/B. The 2.6.x lag turned out to be Bluetooth output buffering, not the app.

## 2.6.2 — 2026-10-10 · Latency fix candidate
- (Reverted in 2.7.0) The AudioContext ran at the **device sample rate** instead of a forced 48 kHz. Forcing a rate the hardware isn't running at makes the browser add a resampling buffer (about 330 ms of output latency measured).
- The latency panel has an AUDIO RATE switch (DEVICE / 48k / 44.1k, reloads the page) for A/B testing.
- At 44.1 kHz the model tube is about 8% longer, so formants sit about 8% lower (closer to an adult male).
- MIDI notes now appear in the latency panel; every note-on is logged.

## 2.6.1 — 2026-10-09 · Latency probe
- On-screen LATENCY panel (Shift+L toggles) splits press → sound into input / sched / build / device, plus main-thread long tasks and frame gaps. Every result is also logged to the console as `[LAT]`.
- AudioContext explicitly requests `latencyHint: 'interactive'`.

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
