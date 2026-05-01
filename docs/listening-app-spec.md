# Listening Room — Application Specification

The Listening Room is a web-based ear-training tool for the [Vodechord](./vodechord-concept.md) project. It exists to ground Vodechord's design decisions in actual listening rather than in abstract concept-doc language.

**Guiding design brief:** *The goal is to learn what a good reference for these effects is, to help us understand and develop great sound contours on a great sound.*

Every exhibit in the site must answer: **does this help the listener hear what "good" sounds like for this feature?** If it doesn't, it doesn't belong.

---

## Audience

Primary audience is the Vodechord project author — i.e., the person making design decisions about which acoustic features matter, which controls to expose, and what quality bar to target. Secondary audience is any curious musician, producer, vocalist, or voice-science hobbyist who stumbles on the site.

The site does not assume prior knowledge of voice-science jargon. It introduces terms in plain English first, then optionally in technical terms. Formulas and equations are not used. Spectrograms and waveforms appear only when they genuinely aid listening (rarely).

The site does assume the listener will use headphones. It says so clearly at the top.

---

## Interaction Model

The site operates on a spectrum from **passive listening** to **active parameter control**, exhibit by exhibit.

### Level 1 — A/B comparison (every exhibit)

Every exhibit has at least two audio clips side-by-side: "without the feature" vs "with the feature," or "low value" vs "high value." Listeners toggle between them instantly with no re-loading delay. Loop playback is default.

### Level 2 — Parameter sweeps (most exhibits)

Many exhibits add a discrete sweep across the feature's range — e.g., five pre-rendered clips at jitter values of 0, 3, 7, 12, and 20 cents. A single slider or button row switches between them. Again: no re-loading, instant toggle, loop by default.

### Level 3 — Real-time Web Audio synthesis (select exhibits only)

For features where real-time control is essential to understanding — jitter, vibrato, spectral tilt, grit, singer's formant, microtiming — the exhibit synthesizes audio in the browser via the Web Audio API. A single sustained vowel or short phrase plays in a loop. The listener drags a slider; the sound changes immediately. The loop doesn't re-trigger.

Level 3 is expensive to build and is reserved for features where the *continuous shape* of the parameter matters. Discrete A/B samples at fixed values (Level 1/2) are fine for features where a handful of settings convey the range.

### No Level 4

There is deliberately no "sequencer," "composer," "build your own voice" feature. The site is not a toy, not a demo for Vodechord, not a product. It is a listening tool. Scope discipline here is part of shipping.

---

## Top-Level Structure

```
Listening Room
├─ Prelude / How to use this site
├─ Section 1 — The Source (the "buzz")
│   ├─ Pitch (F0)
│   ├─ Jitter
│   ├─ Shimmer
│   ├─ Harmonics-to-Noise Ratio (HNR / breathiness)
│   ├─ Spectral Tilt
│   └─ Nonlinear Phonation (grit: growl, fry, sub-harmonic)
├─ Section 2 — The Filter (the "shape")
│   ├─ Vowels and the first two formants
│   ├─ The singer's formant (presence / ring)
│   ├─ Nasals and anti-resonances
│   ├─ Fricatives and plosives (consonant character)
│   └─ Formant transitions (co-articulation)
├─ Section 3 — Expression & Musicality
│   ├─ Vibrato (depth, rate, delayed onset)
│   ├─ Portamento & pitch slides
│   ├─ Microtiming ("feel") — consonants-ahead vs vowels-behind
│   ├─ Energy / intensity (crossfade between soft and pressed)
│   └─ Breath as texture
├─ Section 4 — The Synthetic Voice Lineage
│   ├─ The original Voder (1939)
│   ├─ LPC vocoders (Speak & Spell, Bitspeek, PO-35)
│   ├─ Klatt synthesizers (MITalk, DECtalk)
│   ├─ Classical vocoders (Sennheiser, EMS, Korg, Roland)
│   ├─ Talk box (Peter Frampton, Roger Troutman, Daft Punk)
│   ├─ Auto-Tune as an instrument (Cher, T-Pain, Travis Scott)
│   └─ Modern neural SVS (DiffSinger, CeVIO, Synthesizer V) — what Vodechord is *not*
├─ Section 5 — The Vodechord Target Sound
│   ├─ Affinity profile references
│   │   ├─ Close-Mic Intimate
│   │   ├─ Broadcast Authority
│   │   ├─ Chest-Belt Powerhouse
│   │   ├─ Weaponized Grain
│   │   └─ Clean Synthetic
│   └─ Composite listening — the full stack demonstrated together
└─ Epilogue / Open questions
```

---

## Exhibit Template

Every exhibit page follows the same structure so the listener knows what to expect:

1. **Title.** The feature name in plain English, optionally with technical term in parens.
2. **One-line description.** What this feature *is*, in one sentence.
3. **Why it matters for Vodechord.** Two or three sentences connecting this feature to a Vodechord design decision.
4. **Listen.** The audio content. A/B, sweep, or real-time depending on the feature.
5. **What to listen for.** Plain-English guidance on what the listener's ear should be doing.
6. **Reference recordings.** Where this feature shows up in famous / accessible music, with timestamps and links where possible.
7. **Further reading.** One or two links to deeper technical sources. Optional.

---

## Section-by-Section Detail

### Prelude / How to use this site

- State the headphones assumption.
- Name the guiding question (what does "good" sound like for this feature?).
- Brief pointer to the Vodechord project for context.
- Warn that some exhibits are pre-rendered, some are real-time synthesis, and the real-time ones require a browser with Web Audio support (every modern browser).
- Accessibility notes: all audio has transcripts or descriptions where applicable; keyboard navigation works throughout.

### Section 1 — The Source

This section is about the glottal pulse and its properties. Everything here happens *before* the vocal tract shapes it.

**Pitch (F0).**
- Level 2 exhibit.
- Sweep of sustained vowels at F0 = 80, 120, 180, 260, 440 Hz on the same vowel (ɑ).
- What to listen for: register, perceived gender (trickier than it sounds), the way timbre changes across F0 even with identical filter shape.
- References: speaking vs. singing F0 ranges, operatic ranges, low-male radio voice.

**Jitter.**
- Level 3 exhibit (real-time slider).
- Sustained ɑ vowel at F0=180 Hz. Slider from 0 to 25 cents of random cycle-to-cycle pitch deviation.
- What to listen for: at 0, clinically dead. ~5 cents, alive. ~12 cents, starting to hear the wobble. ~20+, pathological.
- References: healthy speech 0.5–1%, trained singers slightly higher on expression, pathological voices much higher.

**Shimmer.**
- Level 3 exhibit.
- Same setup, slider for amplitude irregularity 0 to 3 dB cycle-to-cycle.
- What to listen for: tonal "breathing" at small values, wobble at large.
- Often paired naturally with jitter — a combined Level 3 exhibit with two sliders would let the listener hear their interaction.

**Harmonics-to-Noise Ratio (HNR).**
- Level 3 exhibit.
- Sustained vowel with a knob that crossfades between pure harmonic source and breath-added source.
- What to listen for: ASMR whisper at low HNR, golden trained voice at high. The middle is where most natural speech lives.
- References: Billie Eilish (very low HNR), Morgan Freeman (high HNR), any opera singer (very high).

**Spectral Tilt.**
- Level 3 exhibit.
- Slider controls the rolloff slope of harmonics above F0.
- What to listen for: shallow tilt = pressed, bright, belted. Steep tilt = breathy, soft, intimate. Same vowel, same pitch, wildly different character.
- References: belted chorus vs. verse of the same pop song.

**Nonlinear Phonation (grit).**
- Level 2 exhibit for each mode (growl, fry, subharmonic), possibly Level 3 for the amount knob.
- What to listen for: the difference between each mode. Growl is fast modulation. Fry is irregular pulses at very low F0. Subharmonic is a period-doubling lock that sounds like a sub octave.
- References: Joe Cocker (growl), Britney Spears' "Oops" intro (fry), Tom Waits (all of the above).

### Section 2 — The Filter

This is about vocal-tract resonances — what makes a vowel an "ah" vs "ee" vs "oo," and what makes a voice sound like *this voice* rather than some other voice.

**Vowels and the first two formants.**
- Level 2 exhibit.
- Same source (LF pulse at F0=150) filtered through F1/F2 combinations for each major English vowel.
- Include a **Level 3 vowel space explorer**: drag a 2D cursor on an F1/F2 plane; vowel morphs in real time. This is perhaps the single most powerful pedagogical exhibit in the site.
- What to listen for: vowels are just F1/F2 coordinates. Everything else (consonants, pitch, breathiness) layers on top.

**The singer's formant (presence / ring).**
- Level 3 exhibit.
- Sustained vowel with a narrow band boost around 2800–3200 Hz, slider for amount.
- What to listen for: at 0 dB, the voice sits back in a mix. At +6 dB, it *rings* and cuts through without getting louder. This is genuinely magical once heard.
- References: classical opera singers vs. speaking voice of the same singer.

**Nasals and anti-resonances.**
- Level 2 exhibit.
- "ma" vs "ba" vs "na" vs "da." Same vowel, same pitch, just the consonant changes.
- What to listen for: the dark, "hollow" quality of nasals comes from anti-resonances (zeros) in the transfer function, not just added nasal formants.

**Fricatives and plosives (consonant character).**
- Level 2 exhibit.
- Isolated fricatives and plosives at various energies. "s" vs "sh" vs "f" vs "th."
- What to listen for: these are noise, not pitched. They carry almost all the "articulation intelligibility" of speech.

**Formant transitions (co-articulation).**
- Level 2 exhibit.
- Short syllables demonstrating the difference between abrupt phoneme boundaries (robotic) and smooth formant glides (natural). Could compare a PSOLA-concatenated "cat" to a formant-interpolated "cat."
- What to listen for: speech is mostly transitions, not steady states. The "vowel" in "cat" is only stable for about 60 ms.

### Section 3 — Expression & Musicality

This is about the musical dimensions that turn filtered sound into performance.

**Vibrato.**
- Level 3 exhibit.
- Sustained note, sliders for depth (0–80 cents), rate (0–8 Hz), and onset delay (0–800 ms).
- What to listen for: delayed onset is the hallmark of trained singing; constant vibrato from attack is what amateur and synthetic voices do.
- References: Whitney Houston's held notes (delayed, deep), opera (delayed, fast).

**Portamento & pitch slides.**
- Level 2 exhibit.
- Two-note phrase with various slide durations and shapes.
- What to listen for: the "carrier slides but filter holds" principle. Also scoop-in and fall-off ornaments.

**Microtiming ("feel").**
- Level 3 exhibit.
- A short four-bar phrase plays against a drum loop. Slider shifts consonant placement from -40 ms (ahead) to +40 ms (behind).
- What to listen for: the same notes at the same pitches feel *urgent* or *laid-back* depending on a few dozen milliseconds of consonant placement.
- References: Frank Sinatra (behind), Michael Jackson (often ahead), trap rap (varies wildly within a single bar).
- **This is the exhibit most central to the "pocketing within a groove" thesis that surfaced during concept development.** It deserves extra design attention.

**Energy / intensity.**
- Level 2 exhibit.
- Three or four pre-rendered versions of the same phrase at low / mid / high energy.
- What to listen for: energy changes timbre (tilt, presence, grit) as much as volume. A belted vowel is spectrally different from a whispered one, not just louder.

**Breath as texture.**
- Level 2 exhibit.
- Phrase with and without explicit breath events between phrases, plus a rhythmic breath example.
- What to listen for: breath removed = clinical / fake. Breath added, placed well = alive.

### Section 4 — The Synthetic Voice Lineage

This is the project's intellectual and aesthetic lineage, told through audio. Each sub-section is a Level 1 exhibit with one or two key references.

**The original Voder (1939).** A rare recording of Homer Dudley's operator-controlled voice machine. Historically the direct ancestor of Vodechord.

**LPC vocoders.** Speak & Spell, Bitspeek, Teenage Engineering PO-35. These are the specific technical lineage Vodechord draws on.

**Klatt synthesizers.** MITalk, DECtalk, Stephen Hawking's voice. Formant-synthesis ancestors.

**Classical vocoders.** Sennheiser VSM201 (Kraftwerk), EMS 5000, Korg VC-10, Roland VP-330 and SVC-350. Every "vocoder" sound in electronic music traces back here.

**Talk box.** A speaker in the mouth modulating a carrier — not a vocoder despite the common confusion. Peter Frampton, Roger Troutman, Daft Punk.

**Auto-Tune as an instrument.** Cher's "Believe," T-Pain, Travis Scott. The moment when pitch correction became a creative tool.

**Modern neural SVS.** DiffSinger, CeVIO, Synthesizer V, Vocaloid. Included explicitly to demonstrate what Vodechord is *not* trying to sound like, and why. Each example is followed by a note on what the synthetic character of the voice does not provide that Vodechord's instrument model offers.

### Section 5 — The Vodechord Target Sound

The payoff section. This is where the listener hears what the full Vodechord vision sounds like when everything works together.

Two parts:

**Affinity profile references.** For each of the five affinity profiles (Close-Mic Intimate, Broadcast Authority, Chest-Belt Powerhouse, Weaponized Grain, Clean Synthetic), a curated collection of real-world vocal recordings that exemplify the archetype. No synthesis here — this is *human* voices that Vodechord is aiming to be able to evoke. Each profile has 3–5 references with specific timestamps and a short description of which features of the voice are load-bearing for the profile.

**Composite listening.** One or two generated examples — built using the same Python / DSP scripts that power the Level 3 exhibits elsewhere — that demonstrate the full stack at work. Source-filter synthesis, humanity controls dialed in, singer's formant on, vibrato with delayed onset, microtiming feel, and a delivered lyric. This is the aspirational "what Vodechord wants to sound like" exhibit. It is explicitly marked as a design target, not a product demo — the real Vodechord instrument does not exist yet.

### Epilogue / Open Questions

A short closing page that names what we learned by building and listening to this site, what remained unclear, and what design decisions on Vodechord are now easier or harder. Written as the site matures; a placeholder lives here until then.

---

## Non-Goals

- **Not a textbook.** No exhaustive coverage of voice science. Only features relevant to Vodechord.
- **Not a demo.** The site never attempts to sell Vodechord or show off its capabilities. Vodechord may not even exist as a working instrument when the site launches.
- **Not a musical composition.** Exhibits are listening specimens, not songs.
- **Not a social product.** No user accounts, comments, likes, saves. A pure reference site.
- **Not a voice cloning tool.** No upload-your-voice feature. The site does not process user audio in any way.
- **Not a generator.** No "type text, hear voice" feature at the site level. That's Vodechord itself; this site exists to inform Vodechord's design, not to preview it.

---

## Success Criteria

The site succeeds if, having gone through it end to end, the author (or any careful listener) can:

1. Identify each acoustic feature by ear in an unfamiliar recording.
2. Describe what "too little" and "too much" of each feature sounds like.
3. Name at least one existing vocal recording that exemplifies each feature in an interesting way.
4. Have a point of view, informed by listening, on which features Vodechord should expose as primary controls versus hide in the synthesis layer.
5. Describe, in concrete sonic terms, what the five affinity profiles are aiming for.

If the author, after months of listening, ends up revising the Vodechord concept doc because their ears disagreed with their original design instincts, the site has *fully* succeeded.

---

## Deferred Decisions

Open questions to resolve as the site develops:

- **Exhibit count per section.** Depth of each section is still flexible — start with one or two exhibits per feature and grow as time permits.
- **Generated-vs-curated mix per feature.** Some features may resist in-browser synthesis (grit modes in particular). For those, we lean on curated references. The audio catalog documents the choice per exhibit.
- **Mobile support.** Level 3 exhibits require careful design for touch. Initial release can be desktop-first; mobile polish in a later phase.
- **Internationalization.** English-only for v1. The site's language matches Vodechord's v1 language scope.
- **Analytics.** None in v1. If usage data becomes interesting, we revisit with privacy-respecting options.
