# Mr Voice VTM 3.0.0 — Release notes

**2026-10-22 · Web release.** Replaces 2.14.0, the test release.

3.0 is the sound-engine release. We traced the pops, clicks, lip chirps and stop distortion heard through 2.x to their causes and fixed them in the model itself, not by masking them with fades, gates or filters. Each fix was tested on its own, by ear, before it went in. Phrases, pads, MIDI and the editor work as before.

## Highlights
- No pop going into a rest, no "air hose" onset, and no lip chirp.
- Cleaner stops (d, g, t, k), and cleaner nasals after stops ("I said NO!", "and now").
- Fricatives and stop bursts are no longer flattened by the output limiter, so s, ʃ, f, θ and ð each have their own level again.
- The mouth moves in step with the breath: the voice engine now runs the tongue, jaw and lips on the audio clock.
- New start-up voice: F0 192 Hz, LUNG 0.25, TENSION 0.21, VIBRATO 0, VOICE SIZE F (14.7 cm).
- The Intelligibility Bench renders through the same playback path as the app.

## Engine changes
Each one was a DEV toggle in 2.14.2–2.14.7 and approved by ear before it was folded in.

1. **Ramps start from now.** Every control change (phrase, knob, MIDI, phoneme bank, master volume) now ramps from the value it actually has at that moment. Before, the browser started each ramp from the end of the previous one, often tens of ms earlier, so 40–75% of the change landed at once.
2. **The source moves every sample.** Lung, fold closure, breath and fricative noise used to move in 2.7 ms stairs (128 samples). They now follow their ramps every sample. The ramps and speeds are unchanged.
3. **The shape moves every sample.** The tract shape now glides across each 128‑sample block instead of jumping once per block. This cured the lip chirp.
4. **Frication is bounded by its pressure.** The noise jet can't move faster than the lung pressure behind it allows. Its gain is re-set (−32 dB), so a held /s/ sits just under the limiter. The hard on/off switch for fricative noise is now a gradual onset.
5. **Stop context.** A stop just before another stop or a nasal is held without a burst of its own, as in speech: "gold‿toy", "said‿NO", "and‿now", "take‿my". The next sound takes over from the closure.
6. **Shape in sync.** During phrases the voice engine runs the tongue, jaw and lips itself, timed together with the source. They used to follow the screen about 23 ms late. It uses the same `intentToDiameters` the diagram uses, so there's one definition, not a copy.
7. **Burst recipe.** Stop closures and releases no longer get the extra lung boost (×1.12 to build, ×1.28 to surge). Burst noise is ×0.3, and the tongue releases a little more slowly (5 ms instead of 2.5 ms). Those boosts had made up for the 23 ms lag in fix 6.

## What was wrong, and how we fixed it
- **"Digital", repeating breath and /h/** (fixed in 2.10.1, already in 2.14.0).
  - **Cause:** the noise generator had collapsed into a 10,466‑value loop.
  - **Fix:** a proper generator (mulberry32).
- **The pop at the end of a voiced group before a rest, after a vowel or the "er" of "there".**
  - **Cause:** ramps started in the past (fix 1), and the rest was delivered as 2.7 ms steps. A 22 ms fold opening became 8 jumps of 12.5% each (fix 2).
  - **Measured:** the click went from 17.5 dB to 7.8 dB; 6 dB and above is heard as a click.
- **"Air hose" onset at the start of a phrase.**
  - **Cause:** the same ramp bug. Lung and folds reached their targets within 1 ms.
  - **Fix:** fix 1. Now heard as "a nice soft starting h".
- **Clicks from the LUNG knob near zero.**
  - **Cause:** airflow follows the square root of pressure, so near zero every knob step is a big relative airflow step, and it arrived as a jump.
  - **Fix:** with fixes 1 and 2, each step is a real 20 ms ramp, applied every sample. *Not re-tested by ear on the knob.*
- **The lip chirp ("metal clown horn") when closing the lips.**
  - **Cause:** the tract shape stepped every 128 samples, a 375 Hz zipper. It was loudest near closure, where the lip output boost rises to 8.8×. It wasn't caused by the lungs: it was captured with lung held steady.
  - **Fix:** fix 3. Confirmed by ear.
- **The clicky /n/ in "I said NO!" and "… and now".**
  - **Cause:** the /d/ release burst fired into the next closure, then the nose opened in 6 ms against the trapped air.
  - **Fix:** fix 5. Confirmed by ear, and the "th" in "Are you there?" is good too.
- **Distortion on d, g and t.** Three causes, each with its own fix:
  - **Lag:** the burst's breath and noise reached the engine about 23 ms before the tongue opened. The shape went via the screen frame, React state, a message and extra smoothing (fix 6).
  - **Hot noise:** fricative and burst noise ran 10–80 dB hot, so the output limiter was setting their level. Its slow recovery also ducked the following vowel for 20–35 ms (fix 4).
  - **Burst boosts:** the boosts tuned to cover the lag were too strong once the lag was gone (fix 7).
  - **Measured:** on "hold-it, take my gold toy." the limiter clipped on 8 blocks before and none after.
- **Voiced closures (b, d, g) clip because the walls are rigid.** Yielding walls were tried as TEST 6. They removed the clip but added a new noise, so they're **not included** (see next steps).

### What didn't work (for the record)
- **2.15, reverted.** It made everything per-sample and changed the lip boost. But its ramp pin (`cancelAndHoldAtTime`) does nothing once a ramp has finished, so the main bug stayed. It also added a low flutter.
- **2.14.1 POP FIX 1 and 2, reverted.** These opened the folds after the lungs and added a viscous term at the folds. They measured better offline but didn't sound better. Our offline measure (airflow drops) didn't match what you hear. From then on we used a 1 ms high-frequency click measure and in-app captures.
- **The DC offset theory.** The output already averages zero.
- **Several burst tweaks made before fix 6** had no effect while the limiter was saturated.

## Other changes since 2.14.0
- **Defaults:** every launch starts at F0 192 Hz, LUNG 0.25, TENSION 0.21, VIBRATO 0 and VOICE SIZE F 14.7 cm. Voice size is no longer remembered between sessions, and the F preset moved from 14.5 to 14.7 cm.
- **Fewer DEV controls:** the RAMP FROM NOW and TEST 1–7 switches are gone; their behaviour is now the only behaviour.
- **DEV › CAPTURE** stays as a developer tool. It keeps a rolling 4 s recording of the output plus source and tract traces, finds clicks, and exports WAV and CSV. File names now carry the version.
- **Stopping a phrase** now opens the folds with a single 80 ms ramp, instead of two ramps that fought each other.
- **Intelligibility Bench:**
  - its new default TRANSITION **app** renders through the live phrase path: the engine's articulators, the shared breath-pressure rule, the phrase pitch contour, stop context and the burst recipe;
  - **live** (2.x) and **class** remain for comparison;
  - its starting values: lung 0.25 and tension 0.21, as in the app; F0 130 Hz and the M voice (16.04 cm), to match its adult-male reference formants.
- The ARC lane is still hidden, as it has been since 2.14.0.

## Things to know
- **Fricative balance has changed.** Measured on held sounds at lung 0.6, against 2.x: s −4.9 dB, ʃ −7.1, θ −15.5, ð −2.8, z +2.0, v +3.2, f about the same. /θ/ is much quieter. Vowels are unchanged.
- **Pads with saved F0 or length edits:** in phrases with a stop before a stop or nasal, the stop now has one cell instead of two. Edits after that point move by one cell, so re-check those pads. Such phrases are also 25–50 ms shorter per affected stop.
- **Hosting:** keep `Phrase Guide.html` in the same folder as the standalone, because the GUIDE ↗ link opens it.
- **Intelligibility Bench:** settings saved in your browser override the new defaults. Set TRANSITION to app and SOURCE AMP to 0.25, or reset.

## What to look at next (recommended, in order)
1. **Re-vote the fricative weights by ear.** They were set while listening through the limiter. Start with /θ/.
2. **Re-vote the stop-burst level** (now ×0.3), and check stop context on more phrases.
3. **Run the Intelligibility Bench on 3.0:** TARGETS, TRANSITIONS, then a CONFUSION round. Save the result as the new baseline.
4. **Output headroom.** Vowels peak right at the limiter threshold (/ɑ/ 1.51 against 1.4). Lower the output scale by about 6 dB and add the level back after the limiter, so it only acts as a safety.
5. **Live playing still moves the mouth the old way.** The phoneme bank, MIDI phoneme keys and articulator knobs go through the screen-rate path with about 75 ms of smoothing. Move them onto the engine articulators, as phrases now are.
6. **Tension, pitch and output gain** still update every 128 samples. If CAPS words click, make tension per-sample.
7. **Walls (TEST 6): revisit.**
   - **First suspect:** the walls' own resonance, about 39 Hz (stiffness 9×10⁴, mass 1.5), applied along the whole tube.
   - **To try:** walls only in the pharynx and cheeks, with more damping.
   - **Where it is:** the code is kept but off; search `TODO(revisit walls)`.
8. **Nasals.** /n/ in the model has no oral closure, so a held /d/ into /n/ still opens the tongue. A true nasal stop would make "and now" and "said NO" more natural.
9. **Still open from earlier:**
   - Push 2 lights for the phrase pads;
   - one voice controller across phrase, bank and MIDI;
   - bar-quantised pads;
   - phrase export and import;
   - syllable boundaries for legato;
   - the ARC lane redesign.

## Files
- **`Mr Voice VTM3.0.0 (standalone).html`:** the instrument in one file. It works offline; without internet the fonts fall back to the system monospace.
- **`Phrase Guide.html`:** markup, speak and sing rules, stops, key and editor gestures.
- **`Workflows.html`:** three ways to play.
- **`MIDI Guide.md`:** setup, binds and controllers.
- **These notes.** History: `Release Notes 2.2.0-2.8.0.md` and `Release Notes 2.9.0-2.14.0.md`.
- **Engineering record:** `Handoff - Pop Fixes.md` and `Notes - Output Capture.md`.
