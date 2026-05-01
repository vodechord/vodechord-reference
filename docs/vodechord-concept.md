# Vodechord: A Lyrical Vocal Instrument

## Project Vision

Vodechord is a playable vocal instrument built as a Max for Live device for Ableton Live. It sits at the intersection of three historically distinct approaches to synthetic voice — the vocoder (carrier + modulator filtering), the Voder (manual phoneme filter switching), and physical modeling of the vocal tract — and unifies them through modern workflow and UX.

The instrument produces vocals that live in the **uncanny valley by design** — clearly synthetic but musically expressive, in the tradition of Auto-Tune's pitched artifacts, vocoder texture, and talk box intimacy. It is not trying to fool the listener into thinking a real person is singing. It is a new kind of instrument that *speaks and sings*.

### One-line pitch

*A playable vocal instrument for Ableton Live that sings what you type — designed to sing, not to speak.*

### What makes this different

- **Unlike a vocoder:** no live vocal modulator input required. The user doesn't need to sing in real time to produce output. Lyrics and syllable sequences drive the filter shaping.
- **Unlike AI voice clones:** the user maintains control. This is an instrument you *play*, not a generator that produces a result.
- **Unlike text-to-speech:** musical expression — pitch, timing, energy, vibrato, articulation — is the primary concern, not intelligibility.
- **Unlike the original Voder or Waveboy filter discs:** the workflow is fast, visual, and integrated into a modern DAW environment.

### What Vodechord intentionally is not

The current research frontier in synthesized voice is neural / diffusion-based singing voice synthesis (DiffSinger, DiTSinger, HiddenSinger, Everyone-Can-Sing, TCSinger, and similar systems). These produce increasingly convincing singing from text + melody inputs, often zero-shot from a short reference.

**Vodechord is deliberately not on that path.** A neural singing-voice synthesizer is a generator: you describe what you want and get a finished vocal. Vodechord is an instrument: you *play* it, and every acoustic parameter is a knob you can grab. That choice has consequences — Vodechord will never sound as "realistic" as a good diffusion model, and it requires more engagement from the user. In exchange, it offers control, editability, interpretability, reproducibility, zero hallucination, offline operation, and a clear relationship between input action and output sound.

This is a different product, serving a different creative need. The doc stays focused on the instrument model and resists the gravitational pull of the neural frontier.

### Language scope (v1)

Vodechord v1 ships **English-only**. The synthesis layer, edit model, file format, and UI are all language-agnostic, but the voice-to-text model, the grapheme-to-phoneme dictionary (CMUDict), the recording session prompts, and all default characters target North American English. Adding additional languages later is an architectural extension, not a rewrite — but it is explicitly out of scope for v1.

---

## Core Architecture

Vodechord has two distinct layers:

- **Synthesis Layer (internal):** the DSP engine that actually produces sound. Works at the phoneme level because that's the natural resolution for LPC-based formant synthesis.
- **Edit Layer (user-facing):** what the musician sees and manipulates. Syllables are the atomic unit here. Users almost never think about phonemes directly; the synthesis layer handles that.

This distinction matters throughout the rest of the document.

### Source-filter model

Vodechord is built on the classical source-filter model of vocal production, with every acoustic feature explicitly mapped to one layer or the other. This is not just an internal organizing principle — it is also how user-facing controls are grouped, so users learn to think about voice the way voice actually works.

- **Source (the "buzz"):** the glottal pulse. Pitch, spectral tilt (breathy ↔ pressed), jitter (pitch wobble), shimmer (amplitude wobble), harmonics-to-noise ratio (HNR), and nonlinear phonation (growl, fry, creak) live here. These features drive the *personality* of the voice — intimacy, power, grit.
- **Filter (the "shape"):** the vocal tract's resonances. Formants (F1–F5), formant transitions, nasal coupling, and the singer's formant live here. These features drive *identity and articulation* — what vowel is being sung, whose voice it sounds like, and whether it cuts through a mix.

The synthesis architecture:

- **Exciter (source):** a harmonically rich signal that serves as the raw vocal energy. This can be a synthetic glottal-pulse source (LF model), classic synthetic oscillators (saw, square, noise), the user's own recorded voice samples built into a playable carrier, or any audio routed into the plugin from another Ableton track. All sources are modulated by jitter, shimmer, and HNR controls downstream.
- **Phoneme filter library (synthesis layer):** a database of filter configurations — formant frequencies (F1 through F3 explicitly, with shaping above), bandwidths, spectral envelopes, amplitude envelopes, and excitation type flags — indexed by phoneme. Each entry defines excitation type (voiced, noise, burst, mixed), filter shape, amplitude envelope, and transition behavior. This is the substrate; users don't edit it directly during composition.
- **Syllable edit layer (user-facing):** a sequence of syllable events, each backed by a phoneme sequence from the synthesis library.
- **Phrase engine:** a sequencer that coordinates syllable events with pitch, timing, and energy data to produce the vocal output.

### Signal flow

```
Lyrics (text) ──→ Syllable Segmentation ──→ Syllable Event Array
                                                     │
                                           (each event expands to a
                                            phoneme sequence internally)
                                                     │
                                                     ▼
MIDI / Performance ──→ Sequencer ─────────→ Driver ──→ FILTER ──→ Output
                                                         ▲        (Vodechord Effect /
Exciter (carrier) ──→ Voice/Audio Source ────────────────┘         Lyrical Audio)
           │
     (jitter / shimmer /
      HNR modulation)
```

### Underlying technology: LPC + Klatt-style formant synthesis

The synthesis layer is primarily built on LPC analysis (the same core technology behind Sonic Charge's Bitspeek and the Teenage Engineering PO-35 Speak) for parameter extraction from recorded voice, and Klatt-style parallel bandpass resonators for the filter bank. This hybrid approach — LPC all-pole filters where the data came from recording, parallel BP resonators where we're synthesizing from scratch — gives us both accuracy on real voice data and clean, predictable behavior when generating from nothing.

### Humanity controls: jitter, shimmer, HNR

Perfectly regular pitch and amplitude are the fastest route to an inhuman-sounding voice. The ear is extraordinarily sensitive to tiny cycle-to-cycle variations — jitter (pitch) and shimmer (amplitude) — and controlled irregularity reads as "alive," while zero reads as "synthetic" and excessive reads as "damaged."

Vodechord exposes:

- **Jitter:** sub-audio-rate random pitch modulation, ±5 to ±15 cents typical. Default non-zero for all characters.
- **Shimmer:** sub-audio-rate random amplitude modulation, ±0.5 dB typical.
- **HNR (breath / clarity):** balance of harmonic vs. aperiodic energy. High HNR = clear, resonant, "golden"; low HNR = breathy, grainy, whispered.

Each is a simple macro (one knob per-character, automatable per-phrase). They affect the source layer only; formants are unaffected.

### Singer's formant / presence

A single **Presence** macro boosts a narrow band at approximately 2800–3200 Hz (Q ≈ 5–10). This is the singer's formant — the resonance cluster that lets classically-trained voices cut through an orchestra, and the region of peak human hearing sensitivity. Increasing Presence makes a voice "ring" and cut through a mix without raising overall level. Decreasing it pulls the voice back and intimate.

### Spectral tilt

A single **Tilt** macro controls the rolloff slope of harmonics above F0 in the source layer. Shallow tilt = pressed, bright, powerful. Steep tilt = breathy, soft, intimate. This single knob spans most of the distance between a belted chest voice and a close-mic whisper.

### Nonlinear phonation (grit)

Growl, vocal fry, creak, subharmonics, and diplophonia are genre-defining features (blues, metal, hip-hop, soul) and essential for expressive range. Vodechord models them at the source layer:

- **Growl:** FM or ring modulation of the glottal pulse with a low-frequency modulator.
- **Subharmonic lock:** a sub-octave source locked to F0, mixed in below the main pulse.
- **Fry / creak:** irregular low-rate pulses replacing the regular glottal cycle, typically at low F0.
- **Amount and mode** are exposed as a single "Grit" parameter with a mode selector.

These are destructive-sounding by design. The distinction between expressive grit and damage is fine control — which is why these live as continuous automatable parameters rather than on/off switches.

---

## The Character System

### Vocal identities

A **Character** is a complete vocal identity — like an instrument in an orchestra. Each character consists of a full set of phoneme filter data derived from a user's recorded voice, plus any recorded syllables captured during the session, plus per-character defaults for the humanity controls (jitter, shimmer, HNR, tilt, presence, grit) that capture the recorded voice's baseline personality. Users build a roster of characters; composition becomes *casting* — assigning characters to tracks the way you assign instruments.

### Character recording session

The recording session is a guided wizard. It walks the user through **every recording needed to make a great-sounding character**, end to end. The wizard's job is to make the full session feel manageable and clear.

The session covers:

1. **Calibration.** Short ambient + warm-up pass to set input levels, identify the user's natural pitch range (**natural register**, captured per character), confirm signal quality, and measure baseline jitter/shimmer/HNR values for the character.
2. **Full phoneme inventory.** Every phoneme in the character's language (English in v1: the full ARPABET set, ~39 phonemes). Each recorded at **three energy levels**: low (intimate/breathy), medium (conversational), high (belted/intense).
3. **Common syllable set.** A curated list of high-frequency English syllables (a few hundred, including all common CV, VC, and CVC patterns) at the three energy levels.
4. **Pitch range samples.** A small set of sustained vowels and chosen syllables sung at low, mid, and high points of the natural register. Used to stabilize the recorded-voice carrier across pitch.
5. **Personality passes (optional but encouraged).** A few free-form takes — a sigh, a laugh, a hummed line, a spoken sentence — captured as character material the user can drop into compositions as ornamentation. Also a short **grit sample** (growl or fry) if the character has one, for driving the nonlinear source modes.

The wizard shows progress, time remaining, pause/resume across sessions, and flags retakes needed for clipping, off-pitch, off-phoneme, or too-quiet recordings.

### Default characters as affinity profiles

Default characters ship with the plugin for immediate use. Rather than generic "male / female / neutral" starting points, the defaults are built as **affinity profiles** — archetypes defined by their acoustic signature rather than their demographic:

- **Close-Mic Intimate** — low F0, breathy, steep spectral tilt, high jitter, audible breath sounds. Proximity-response maximized.
- **Broadcast Authority** — very low F0, very high HNR, tight low-formant spacing, slow prosody. The "trusted narrator" voice.
- **Chest-Belt Powerhouse** — strong singer's formant, stable HNR across dynamics, shallow tilt, wide dynamic range.
- **Weaponized Grain** — low HNR, audible fry/creak, narrow dynamic range. Character voice in the Waits / Cohen / Armstrong lineage.
- **Clean Synthetic** — reference voice with minimal humanity controls; explicitly instrument-like, not human-imitating.

Each profile is a starting point, a preset combination of synthesis settings, and a target to record against if the user wants to build their own version of that archetype. They also serve a structural role: **they are the fallback substrate** for user-created characters. If a user-built character is missing a phoneme, the synthesis layer falls back to the closest-matching default.

### Energy interpolation

The three energy tiers enable smooth intensity control. An energy/intensity curve applied over a phrase crossfades between low, medium, and high filter sets. Transitions are smooth because real recorded data exists at each level.

### Character file format

Characters are stored and shared as **JSON files** (`.vodechord-char.json`). JSON is readable, diffable, version-controllable, easy to inspect, and trivial to extend.

A character file bundles:

- **Metadata:** name, author, version, creation date, schema version, description, tags, affinity profile reference (if based on one).
- **Natural register:** the pitch range captured during calibration.
- **Humanity baseline:** default jitter, shimmer, HNR, tilt, presence, and grit values for the character.
- **Phoneme table:** entry per phoneme × energy level, containing LPC parameters.
- **Syllable table:** entry per recorded syllable × energy level.
- **Audio asset manifest:** paths or hashes for associated raw audio files.

Top-level schema:

```json
{
  "schemaVersion": "1.0",
  "character": {
    "name": "Breathy Whisper",
    "author": "...",
    "createdAt": "2026-04-19T...",
    "description": "...",
    "tags": ["whisper", "intimate"],
    "affinityProfile": "close-mic-intimate",
    "naturalRegister": { "lowHz": 120, "centerHz": 220, "highHz": 380 },
    "humanity": {
      "jitter": 0.08, "shimmer": 0.04, "hnr": 0.35,
      "tilt": 0.75, "presence": 0.3, "grit": { "amount": 0, "mode": "none" }
    }
  },
  "phonemes": { "AH": { }, "IY": { } },
  "syllables": { "la": { } },
  "assets": { }
}
```

Sharing is a matter of zipping the JSON with its audio folder. Schema versioning allows forward and backward compatibility.

---

## Synthesis Library Structure

*This section describes the internal synthesis layer. End users rarely interact with it directly.*

### Phoneme categories

| Category | Examples | Excitation | Filter Behavior | Envelope |
|---|---|---|---|---|
| Vowels | ah, ee, oo, eh | Voiced (LF pulse) | Formant peaks (F1, F2, F3) | Sustained |
| Plosives | p, b, t, d, k, g | Silence → burst | Gate carrier, trigger transient | Hard stop → fast attack |
| Fricatives | s, sh, f, v, z | Noise (unvoiced) or mixed | Narrow band noise shaping | Sustained noise |
| Nasals | m, n, ng | Voiced | Formant shift + anti-resonances | Sustained, closed-mouth |
| Approximants | r, l, w, y | Voiced | Rapid formant transitions | Glide |
| Affricates | ch, j | Plosive + fricative | Burst → narrow noise | Hard attack → sustained noise |
| Glottal / Breath | h, glottal stop, breath | Noise / transient | Broadband or gap | Transient / ambient |

### Phoneme set

Vodechord uses the full **ARPABET** phoneme set (39 phonemes for North American English), aligned with CMUDict. Every character is expected to have the full set; missing entries fall back to the default character.

### Phoneme data structure

Each phoneme entry contains:

- **Excitation type:** voiced, unvoiced (noise), burst, mixed, silence
- **Formant positions:** F1, F2, F3 frequencies and bandwidths (per energy level)
- **Spectral envelope:** overall spectral shape including anti-resonances for nasals
- **Amplitude envelope:** attack, sustain, release characteristics
- **Transition rules:** how this phoneme glides into / out of adjacent phonemes
- **Noise ratio:** balance of harmonic vs. noise content
- **Raw audio reference:** original recorded sample (optional, for hybrid carrier use)

---

## Lyrics → Phonemes Pipeline

The system translates between **written words**, **phoneme sequences**, and **time-aligned audio**. This pipeline handles both directions.

### Words → phonemes (forward)

1. **CMUDict lookup.** The Carnegie Mellon Pronouncing Dictionary (~134,000 entries, ARPABET). The workhorse. Bundled with the plugin.
2. **Neural g2p fallback.** For out-of-vocabulary words, a small neural grapheme-to-phoneme model trained on CMUDict (g2p-seq2seq, Phonetisaurus, or Sequitur) produces a best-guess phoneme sequence.

Words with multiple valid pronunciations default to the first listed, with alternatives exposed in the syllable inspector.

### Audio → phonemes (reverse)

1. Voice-to-text produces a word-level transcript with confidence scores.
2. CMUDict + g2p convert the transcript to an expected phoneme sequence.
3. **Forced alignment** (Montreal Forced Aligner-style triphone architecture) matches the expected phoneme sequence to the audio, producing time-aligned phoneme and syllable boundaries.
4. Low-confidence words fall through to **library-matching** against the character's own phoneme filter profiles.

Published research has shown retraining MFA on sung audio significantly improves performance — a viable direction post-v1.

---

## Hierarchy & Editing Model

### Nested structure

```
Phrase (primary arrangement unit)
  └─ Word
       └─ Syllable (atomic editable unit)
            └─ [internal sub-structure: phoneme sequence,
                consonant transients, vowel body, breath
                sounds, glottal stops]
```

### Why syllables are atomic

Syllables are the unit musicians actually think in. Melisma is defined as multi-note runs on a single syllable. Lyrics are written and sung syllable-by-syllable. Rhythmic placement, pitch assignment, and articulation all happen at syllable boundaries. Phonemes still exist underneath every syllable — and power users can pop open a syllable's inspector to tweak them — but the primary editing experience stops at the syllable.

### Phrases as the primary unit

**Phrases are the timeline-editable, arrangeable, triggerable unit** — equivalent to clips in Ableton's workflow.

### Drill-down editing

- **Phrase level:** drag, stretch, move the whole block.
- **Word level:** adjust individual word timing within the phrase.
- **Syllable level:** the atomic level — timing, pitch, energy, transition speed, articulation.
- **Syllable inspector (advanced):** exposes phoneme sequence, formant data, noise ratios, transition envelopes.

All child-level timing is *relative* to the parent.

---

## Workflow

### Primary workflow: sing → capture → edit → refine

1. **Record a take.**
2. **Automatic analysis.** Pitch detection, syllable segmentation, voice-to-text (always on, async), energy profiling.
3. **Editable clip.** Result is a parametric phrase, not audio.
4. **Refine.**

### Voice-to-text: always on, designed to fail well

Voice-to-text runs on every take, from day one. It does not need to be accurate.

- **Never block.** Clip is playable before V2T returns.
- **Never fail silently.** Garbage output → placeholder labels on still-playable syllables.
- **Always produce vocal content.** Mumble tracks always yield a playable editable phrase.
- **Confidence is visible.** Three-state typography (see below).
- **Correction is frictionless.** Inline text edit; timing preserved.
- **Re-transcribe is one click.**

### Mumble / sketch takes

Nonsense vocals are first-class input. Even gibberish produces real syllables, pitch, timing, and energy. Later, typed lyrics can replace the scaffold while inheriting the captured performance.

### Comping across takes

Multiple takes stack in Ableton's take lanes. Consensus / blend mode averages across takes; syllable-level cherry-picking has no crossfade artifacts because we're working on parameter data.

### Alternative workflow: type → play → sequence

Paste lyrics → system distributes syllables on the timeline → user plays melody via MIDI → each note-on advances to the next syllable.

---

## Syllable Segmentation Strategy

### Multi-stage pipeline

1. **Onset + nucleus detection (acoustic).** Vowel-like energy marks syllable nuclei. Works on *any* take, including pure mumble.
2. **V2T pass (linguistic).** Word-level transcription with confidence scores.
3. **Forced alignment (combined).** CMUDict + V2T transcript produces expected phoneme sequence; MFA-style alignment snaps boundaries to acoustic landmarks.
4. **Library matching (fallback).** Low-confidence regions get their LPC parameters matched against the active character's phoneme library, producing a best phoneme-sequence guess.
5. **Resolution.** Each syllable ends up with timing (always confident), a phoneme sequence (always present, varying confidence), and a lyric label.

### Why this should work

- Never depends on V2T being right (acoustic syllable detection holds the floor).
- Never depends on typed lyrics (library matching produces phoneme sequences from audio alone).
- The character library is our own data, making matching more accurate than a generic acoustic model.
- Each stage has a known failure mode and a known fallback.

### What's hard

- Legato / melismatic passages blur syllable boundaries; user can split / merge manually.
- Sung vowels at unusual pitches confuse models trained on speech; sung-audio retraining is a post-v1 path.
- Rapid consonant clusters in rap benefit from a faster onset-detection mode; a delivery-style hint from the user tunes the pipeline.

---

## Confidence Visualization

Syllable timing comes from acoustic analysis and is therefore always reliable. Labels — what was uttered — gracefully degrade:

| State | Source | Typography | Behavior |
|---|---|---|---|
| High-confidence lyric | V2T returned with high confidence | Bold black, full word | Treat as ground truth |
| Low-confidence phonetic guess | V2T uncertain; library matching produced best phoneme match | Lighter weight, displayed as phonemes, possibly italic | Invites correction |
| User-edited | User typed or corrected | Bold black with subtle frame / underline / tick | Locked from auto-overwrite |

Hovering a low-confidence syllable reveals alternative phonetic guesses. Re-transcribing preserves user-edited labels.

The principle: **timing is always real; the label gracefully degrades from "what they sang" to "what the closest sound was."** User always has something to grab and edit.

---

## Vocal Register & Carrier Blending

One pitch tier per character. Outside the natural register, a **register-aware carrier blend** keeps the voice singable across the whole MIDI keyboard.

### Register crossfade

- Within natural range: recorded carrier at full strength.
- Moving out of range: recorded carrier fades down on a user-shapeable curve, synthetic carrier fades up with its own ADSR envelopes for amplitude and filter tracking.
- Far outside range: recorded carrier mutes entirely; only synthetic remains.

The further from natural register, the more "instrumental" the voice becomes. **This is intentional** — soaring above the register sounds like the voice opening into a synth lead; descending below sounds like melting into a sub-bass tone.

### Tunable parameters

- **Register width** (per phrase or global)
- **Crossfade curve** (linear, exponential, hand-drawn)
- **Synth carrier ADSR** (independent from voice side)
- **Register lock** (optional safety: refuse notes outside range)
- **Pitch ceiling / floor behavior** (crossfade / transpose / mute)

### UI: register heatmap

Optional colored band behind the pitch lane showing the active character's natural range. Notes outside shade toward "instrument" colors.

---

## Transition Modeling

### Intra-syllable: phoneme → phoneme (consistent)

Rule-based, driven by synthesis library. Each phoneme's data includes formant trajectories for common neighbors. A single global "intra-syllable glide rate" is exposed; per-instance adjustment lives in the inspector.

### Inter-syllable: syllable → syllable (captured & editable)

First-class data field on each syllable:

- **Captured contour.** Measured from audio and stored on record.
- **Curve preset.** For typed lyrics: *legato*, *natural*, *staccato*, *gliding*, *breathy*, *rap*. Or draw a custom curve.

Per-phrase transition default overrides individual syllables non-destructively.

---

## Expression & Performance

### Continuous parameters

| Parameter | What It Controls | Typical Source |
|---|---|---|
| Pitch | Carrier frequency / melody | MIDI notes, pitch bend |
| Energy / Intensity | Crossfade between low/mid/high character tiers | Velocity, mod wheel, automation |
| Vibrato depth & rate | 4.5–6.5 Hz periodic modulation of pitch + amplitude + formant shimmer | LFO, mod wheel, automation |
| Jitter | Sub-audio pitch wobble (humanity) | Per-character default; automatable |
| Shimmer | Sub-audio amplitude wobble (humanity) | Per-character default; automatable |
| HNR / Breathiness | Harmonic vs. aperiodic balance | Per-character default; automatable |
| Spectral Tilt | Breathy ↔ pressed source shaping | Expression controller, automation |
| Presence / Ring | Singer's formant band (2800–3200 Hz) | Expression controller, automation |
| Grit | Nonlinear phonation (growl / fry / sub) with mode selector | Automation |
| Feel (microtiming) | Consonants-ahead (drive) ↔ vowels-behind (laid-back) | Per-phrase |
| Inter-syllable glide | Speed and shape of syllable → syllable transitions | Per-syllable, per-phrase |
| Register blend | Override of natural-register crossfade | Per-phrase, automation |
| Delivery style | Legato singing → spoken → staccato rap | Per-phrase or global |

### Vibrato (default behavior)

Vibrato defaults to **delayed onset**: straight tone for the first 300–500 ms of a sustained note, then vibrato fades in. This mirrors the behavior of trained singers and reads as more musical than constant vibrato. The delay time, depth, and rate are all automatable; delay can be set to zero for instant vibrato when desired.

### Microtiming ("Feel")

A per-phrase "Feel" slider shifts consonant placement relative to the grid. Consonants pushed ahead = driving, urgent. Vowels held behind = laid-back, groove-forward. The shift is small (typically a few dozen milliseconds) but perceptually significant.

### Delivery modes

- **Singing:** syllables sustain, smooth transitions, pitch quantized to MIDI notes.
- **Spoken word:** moderate syllable duration, pitch follows contour.
- **Rap:** short syllables, hard transitions, punchy envelopes, pitch as contour.

### Melisma & articulation

- **Melisma:** one syllable, multiple MIDI notes. Filter holds; pitch moves.
- **Multiple syllables on one pitch:** sequence advances; pitch holds.
- **Portamento:** carrier slides; filters anchored.
- **Dynamics:** velocity / expression crossfades spectral profiles — not just amplitude, but tilt, harmonic richness, and formant definition.

---

## Carrier / Exciter Options

### Synthetic carriers
- **LF glottal pulse (default).** Liljencrants-Fant model; voice-like by design. The canonical source for "instrument-like voice."
- **Classic oscillators.** Saw, square, noise — the vocoder-style options.

Both feed the register-extension carrier and can be used as primary carriers for overtly electronic textures.

### Recorded voice carrier
Built from the character recording session. Phoneme and syllable recordings crossfaded into a continuous source within the natural register.

### Hybrid carrier
Blend of synthetic and recorded. The **uncanny valley dial** controls the mix. Distinct from register-blend (pitch-dependent, automatic) — the uncanny dial is a global character setting.

### External audio
Any audio from another Ableton track becomes the voice source. The synthesis layer shapes it into speech-like patterns.

### Breath as texture

Breath sounds — aspirations, lip smacks, exhales — are first-class character material, not noise to clean out. They can be:

- **Automatic:** emitted between phrases or at punctuation, at a user-tunable density.
- **Triggered:** placed on the timeline as explicit breath events.
- **Rhythmic:** used percussively — breath on the downbeat, exhales as rhythmic punctuation.

The close-mic affinity profile leans heavily on audible breath; the broadcast affinity profile suppresses it. Each character has a "breath amount" parameter.

---

## Technical Platform

### Max for Live

- **`buffer~` / `groove~`:** sample playback and pitch shifting for recorded voice carriers.
- **`pfft~` / `filtergraph~`:** spectral processing and formant filtering.
- **`jsui`:** custom JavaScript UI.
- **Live Object Model (LOM):** clip, take lane, transport integration.
- **MIDI input.**

### Analysis pipeline

| Stage | Method | Status | Speed |
|---|---|---|---|
| Pitch detection | YIN / pYIN | Always on, blocking | Faster than real time |
| Syllable nucleus detection | Spectral energy / formant tracking | Always on, blocking | Faster than real time |
| Voice-to-text | Lightweight speech model (Python sidecar) | Always on, async | Near real time |
| Words → phonemes | CMUDict + neural g2p fallback | On demand | Instant |
| Forced alignment | MFA-style triphone | Always on, async after V2T | Faster than real time |
| Library matching (fallback) | LPC parameter matching against character library | Async, low-confidence only | Faster than real time |
| LPC parameter extraction | Linear predictive coding | Always on, blocking | Faster than real time |

Estimated total analysis time for a 30-second take: **2–5 seconds** blocking; V2T and downstream typically resolve in the same window.

---

## UI Concepts

### Phrase timeline
Phrase blocks with lyric text. Low-confidence words render in distinct style.

### Drill-down editor
Syllable-level events, pitch vertical. Inline text editing on click. Optional register heatmap behind pitch lane.

### Syllable inspector (advanced)
Phoneme sequence, alternative phonetic guesses, per-phoneme parameters, intra-syllable transition rules.

### Karaoke / cue display
Just-in-time lyric display during playback and recording.

### Character manager
Create, name, manage characters. Guided recording wizard. Import/export `.vodechord-char.json`. Affinity profile browser for starting points.

### Expression curves
Overlaid curves on the phrase timeline: intensity, vibrato, tilt, presence, grit, register blend, inter-syllable glide, feel, and other continuous parameters.

---

## Design Principles

1. **Phrases first, syllables second.** The user thinks in phrases and syllables, not phonemes.
2. **Sing to sequence.** The fastest way to build a vocal part is to perform it.
3. **Editable, not destructive.** Every captured performance becomes refineable data.
4. **Fail well, never silently.** Every stage of analysis degrades gracefully.
5. **Classical DSP, human controls.** Vodechord uses source-filter synthesis with every parameter exposed as a knob. We deliberately avoid the neural singing-voice frontier because the project's value is the instrument model, not the generator model.
6. **Characters are instruments.** Building a voice is like building a synth patch.
7. **Humanity is a parameter.** Jitter, shimmer, HNR, tilt, and breath aren't side effects — they're first-class controls.
8. **Out-of-range is a feature.** Notes outside natural register don't break; they transform.
9. **Uncanny by design.** Synthetic-but-expressive, not realistic.
10. **Stay in Ableton.** Use Live's native concepts where possible.
11. **Dry output.** Spatial effects, reverb, post-processing are the user's domain downstream.

---

## Open Questions for Prototyping

- **V2T model choice:** Whisper-small, Whisper-tiny, Vosk, or a sung-vocal-tuned variant?
- **Forced aligner integration:** bundle MFA in a Python sidecar, or implement lighter-weight alignment directly?
- **Sung-audio retraining:** worth investing post-v1 in retraining acoustic models on sung audio?
- **Recording session length:** can we get under 20 minutes for a complete character without sacrificing quality?
- **Jitter / shimmer defaults:** what values feel "alive" without feeling shaky?
- **Grit implementation:** FM vs. ring-mod vs. locked sub-harmonic — which sounds best across the range of voices?
- **Library-matching latency:** how fast can LPC matching run? Precomputed search indices may be needed.
- **Inter-syllable transition presets:** refine the six-curve set from user feedback.
- **Max for Live UI constraints:** how much custom UI is feasible in `jsui`?
- **Confidence threshold tuning:** at what V2T score do we switch from "show as lyric" to "show as phonetic guess"?
- **Affinity profile coverage:** are the five starting profiles the right set?
- **Register crossfade defaults:** what curve and width feels best by default?

---

## Simplification Candidates (Live Tension)

The concept above is the full vision. There is an active design tension — explicitly acknowledged — between this full vision and a simplified v1 that ships sooner, sounds great immediately, and exposes only one or two of the most essential controls. That simplification work is deferred until the Listening Room companion site surfaces enough audio evidence to make the cuts with confidence rather than guesswork.

Candidates for deferral or cut in v1, to be decided by ear:

- Most continuous control parameters beyond pitch and one or two expression dimensions.
- Voice recording wizard (can be v1.5 if default affinity profiles are good enough).
- Forced alignment and library-matching fallback (can be deferred if the workflow pivots to typed lyrics as primary).
- V2T (can be deferred if typed lyrics are primary and recorded-take workflow is v2).
- Rap / spoken delivery modes (Vodechord may be singing-first in v1).
- Comping across takes (only relevant if recording is a primary path).

What stays in v1 regardless:

- Source-filter synthesis architecture.
- LPC + Klatt-style filter bank.
- Default characters (affinity profiles) that sound great out of the box.
- Text-to-voice as the canonical input path.
- Syllable-atomic edit model.
- The one or two expression controls that most change perceived quality (the Listening Room should surface which).
- Dry output principle.
- Max for Live host integration.

---

*Document version: April 23, 2026 — Project renamed Voder → Vodechord. Concept otherwise unchanged from the April 20 revision. Simplification tension acknowledged as a live design question to be resolved by ear via the Listening Room companion site.*
