# Audio Catalog

This document is a structured, exhibit-by-exhibit catalog of every audio example intended to appear in the Listening Room. It is the working spec for audio production and the source-of-truth for rights and sourcing.

Each entry specifies:

- **ID** — stable identifier used in source code and filenames (`s1-pitch-sweep`, `s3-vibrato-delayed-onset`, etc.).
- **Section & Exhibit** — where it appears.
- **Type** — curated recording, generated in-browser (Web Audio API), or pre-rendered from a generation script.
- **Source** — URL, recording timestamp, or generation recipe.
- **Rights** — license / attribution / fair use basis.
- **Status** — `todo`, `in-progress`, `done`, `blocked`.

All entries start as `todo`. As the site develops, entries move through states and are audited periodically for rights compliance.

---

## Conventions

- **Generated examples** are produced by scripts in `/scripts/` that emit WAV files to `/audio/generated/` and are committed as build artifacts or regenerated on CI. Each generation script is deterministic given a seed so examples can be reproduced.
- **Curated recordings** are *never committed as audio files* to the repo. Instead, the site embeds them from their original hosting (YouTube, Spotify, artist pages), links out, or transcribes relevant passages with timestamps. This keeps the repo small and sidesteps most rights issues.
- **Web Audio API exhibits** are not audio files at all — they are synthesized in the browser at play time. Their "source" is the JS/DSP code that implements them, typically in `/src/exhibits/`.

---

## Section 1 — The Source

### Pitch (F0)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-pitch-80` | Generated | `scripts/gen_pitch_sweep.py` — LF pulse, sustained ɑ, F0=80 Hz, 2 sec | MIT (our code) | todo |
| `s1-pitch-120` | Generated | same, F0=120 Hz | MIT | todo |
| `s1-pitch-180` | Generated | same, F0=180 Hz | MIT | todo |
| `s1-pitch-260` | Generated | same, F0=260 Hz | MIT | todo |
| `s1-pitch-440` | Generated | same, F0=440 Hz | MIT | todo |
| `s1-pitch-ref-low-male` | Curated | Reference recording of very low speaking voice (e.g., Barry White interview) — link-out only, no embed | Fair use / link | todo |
| `s1-pitch-ref-soprano` | Curated | Reference opera soprano sustained note — link-out only | Fair use / link | todo |

### Jitter

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-jitter-realtime` | Web Audio | `src/exhibits/jitter.js` — sustained ɑ at F0=180 Hz, slider 0–25 cents | MIT | todo |
| `s1-jitter-00` | Generated | Backup pre-rendered at 0 cents | MIT | todo |
| `s1-jitter-05` | Generated | 5 cents | MIT | todo |
| `s1-jitter-12` | Generated | 12 cents | MIT | todo |
| `s1-jitter-20` | Generated | 20 cents | MIT | todo |
| `s1-jitter-ref-healthy` | Curated | Published voice-pathology demo: healthy voice reference | Academic / link | todo |
| `s1-jitter-ref-pathology` | Curated | Published voice-pathology demo: high-jitter voice | Academic / link | todo |

### Shimmer

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-shimmer-realtime` | Web Audio | `src/exhibits/shimmer.js` — same sustained ɑ, slider 0–3 dB | MIT | todo |
| `s1-shimmer-00` | Generated | 0 dB | MIT | todo |
| `s1-shimmer-05` | Generated | 0.5 dB | MIT | todo |
| `s1-shimmer-15` | Generated | 1.5 dB | MIT | todo |
| `s1-shimmer-30` | Generated | 3 dB | MIT | todo |

### Jitter + Shimmer combined

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-humanity-combined` | Web Audio | Dual-slider exhibit combining jitter and shimmer controls | MIT | todo |

### Harmonics-to-Noise Ratio (HNR / breathiness)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-hnr-realtime` | Web Audio | `src/exhibits/hnr.js` — crossfader between pure-harmonic source and breath-noise source | MIT | todo |
| `s1-hnr-ref-whisper` | Curated | Billie Eilish verse passage (link-out with timestamp) | Fair use / link | todo |
| `s1-hnr-ref-golden` | Curated | Morgan Freeman documentary narration (link-out with timestamp) | Fair use / link | todo |
| `s1-hnr-ref-operatic` | Curated | Cecilia Bartoli sustained aria note (link-out with timestamp) | Fair use / link | todo |

### Spectral Tilt

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-tilt-realtime` | Web Audio | `src/exhibits/tilt.js` — source rolloff slope control, -3 to -18 dB/oct | MIT | todo |
| `s1-tilt-pressed` | Generated | Shallow tilt (-6 dB/oct) sustained ɑ | MIT | todo |
| `s1-tilt-breathy` | Generated | Steep tilt (-15 dB/oct) same | MIT | todo |
| `s1-tilt-ref-verse-chorus` | Curated | Contemporary pop verse (breathy) → chorus (belted) same song, same singer | Fair use / link | todo |

### Nonlinear Phonation (grit)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s1-grit-none` | Generated | Clean voice reference | MIT | todo |
| `s1-grit-growl-realtime` | Web Audio | `src/exhibits/grit_growl.js` — FM / ring-mod sustained vowel, slider for amount | MIT | todo |
| `s1-grit-fry-realtime` | Web Audio | `src/exhibits/grit_fry.js` — irregular low-rate pulses, slider for amount | MIT | todo |
| `s1-grit-sub-realtime` | Web Audio | `src/exhibits/grit_sub.js` — subharmonic lock, slider for amount | MIT | todo |
| `s1-grit-ref-cocker` | Curated | Joe Cocker "With a Little Help from My Friends" (link-out) | Fair use / link | todo |
| `s1-grit-ref-spears` | Curated | Britney Spears "Oops!... I Did It Again" intro ("Oops") — vocal fry on initial vowel | Fair use / link | todo |
| `s1-grit-ref-waits` | Curated | Tom Waits "Jockey Full of Bourbon" (link-out) | Fair use / link | todo |

---

## Section 2 — The Filter

### Vowels and the first two formants

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s2-vowel-space-realtime` | Web Audio | `src/exhibits/vowel_space.js` — 2D F1/F2 draggable explorer | MIT | todo |
| `s2-vowel-ah` | Generated | F1=730, F2=1090 | MIT | todo |
| `s2-vowel-ee` | Generated | F1=270, F2=2290 | MIT | todo |
| `s2-vowel-oo` | Generated | F1=300, F2=870 | MIT | todo |
| `s2-vowel-eh` | Generated | F1=530, F2=1840 | MIT | todo |
| `s2-vowel-ih` | Generated | F1=390, F2=1990 | MIT | todo |
| `s2-vowel-uh` | Generated | F1=520, F2=1190 | MIT | todo |

### The singer's formant (presence / ring)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s2-presence-realtime` | Web Audio | `src/exhibits/presence.js` — narrow boost at 2800–3200 Hz, Q=8, slider 0 to +12 dB | MIT | todo |
| `s2-presence-off` | Generated | Sustained ɑ with no presence boost | MIT | todo |
| `s2-presence-on` | Generated | Same with +6 dB boost | MIT | todo |
| `s2-presence-ref-pavarotti` | Curated | Pavarotti aria (link-out) — demonstrates classic singer's formant | Fair use / link | todo |
| `s2-presence-ref-speaking` | Curated | Same singer speaking, for contrast | Fair use / link | todo |

### Nasals and anti-resonances

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s2-nasal-ma` | Generated | "ma" syllable with nasal coupling | MIT | todo |
| `s2-nasal-ba` | Generated | "ba" syllable (no nasal) | MIT | todo |
| `s2-nasal-na` | Generated | "na" | MIT | todo |
| `s2-nasal-da` | Generated | "da" | MIT | todo |

### Fricatives and plosives

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s2-fric-s` | Generated | "s" sustained, 1 sec | MIT | todo |
| `s2-fric-sh` | Generated | "sh" sustained | MIT | todo |
| `s2-fric-f` | Generated | "f" sustained | MIT | todo |
| `s2-fric-th` | Generated | "th" sustained | MIT | todo |
| `s2-plos-p` | Generated | Isolated "p" burst | MIT | todo |
| `s2-plos-t` | Generated | Isolated "t" burst | MIT | todo |
| `s2-plos-k` | Generated | Isolated "k" burst | MIT | todo |

### Formant transitions (co-articulation)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s2-coart-abrupt` | Generated | "cat" with abrupt phoneme boundaries (no transition modeling) | MIT | todo |
| `s2-coart-smooth` | Generated | "cat" with formant-interpolated transitions | MIT | todo |

---

## Section 3 — Expression & Musicality

### Vibrato

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s3-vibrato-realtime` | Web Audio | `src/exhibits/vibrato.js` — 3 sliders: depth (0–80 cents), rate (0–8 Hz), onset delay (0–800 ms) | MIT | todo |
| `s3-vibrato-none` | Generated | Sustained note with no vibrato | MIT | todo |
| `s3-vibrato-immediate` | Generated | Same with vibrato from attack | MIT | todo |
| `s3-vibrato-delayed` | Generated | Same with 400 ms delayed onset | MIT | todo |
| `s3-vibrato-ref-whitney` | Curated | Whitney Houston held note (link-out) — delayed onset | Fair use / link | todo |
| `s3-vibrato-ref-opera` | Curated | Operatic sustained note — fast, deep vibrato | Fair use / link | todo |

### Portamento & pitch slides

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s3-porta-instant` | Generated | Two-note phrase, no slide | MIT | todo |
| `s3-porta-slow` | Generated | Same phrase, 200 ms slide | MIT | todo |
| `s3-porta-filter-held` | Generated | Slide with filter anchored on destination vowel | MIT | todo |
| `s3-porta-ref-whitney` | Curated | "I Will Always Love You" climb — iconic portamento | Fair use / link | todo |

### Microtiming ("feel")

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s3-feel-realtime` | Web Audio | `src/exhibits/feel.js` — 4-bar phrase over drum loop, slider -40 ms to +40 ms for consonant placement | MIT | todo |
| `s3-feel-ahead` | Generated | Phrase with consonants pushed 25 ms ahead | MIT | todo |
| `s3-feel-on-grid` | Generated | Same phrase on-grid | MIT | todo |
| `s3-feel-behind` | Generated | Same phrase 25 ms behind | MIT | todo |
| `s3-feel-ref-sinatra` | Curated | Sinatra "Fly Me to the Moon" — behind-the-beat phrasing | Fair use / link | todo |
| `s3-feel-ref-mj` | Curated | Michael Jackson "The Way You Make Me Feel" — ahead | Fair use / link | todo |
| `s3-feel-ref-trap` | Curated | Contemporary trap verse demonstrating rhythmic micro-variation | Fair use / link | todo |

### Energy / intensity

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s3-energy-low` | Generated | Phrase rendered at low-energy character tier | MIT | todo |
| `s3-energy-mid` | Generated | Same at mid tier | MIT | todo |
| `s3-energy-high` | Generated | Same at high tier | MIT | todo |

### Breath as texture

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s3-breath-none` | Generated | Phrase with breath edited out | MIT | todo |
| `s3-breath-natural` | Generated | Phrase with natural breath between clauses | MIT | todo |
| `s3-breath-rhythmic` | Generated | Phrase with breath on downbeats (percussive use) | MIT | todo |
| `s3-breath-ref-billie` | Curated | Billie Eilish ASMR-style breath (link-out) | Fair use / link | todo |

---

## Section 4 — The Synthetic Voice Lineage

All entries in this section are curated references. No generation. Each is a link-out to the original recording with timestamps where applicable.

### The original Voder (1939)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-voder-demo` | Curated | 1939 World's Fair Voder demonstration (Bell Labs Archive; YouTube copies exist) | Fair use / link | todo |

### LPC vocoders

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-speakspell` | Curated | Speak & Spell word examples | Fair use / link | todo |
| `s4-bitspeek` | Curated | Sonic Charge Bitspeek demo | Vendor demo / link | todo |
| `s4-po35` | Curated | Teenage Engineering PO-35 Speak demo | Vendor demo / link | todo |

### Klatt synthesizers

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-dectalk` | Curated | DECtalk Perfect Paul voice | Fair use / link | todo |
| `s4-hawking` | Curated | Stephen Hawking's synthesized voice | Fair use / link | todo |

### Classical vocoders

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-kraftwerk` | Curated | Kraftwerk "The Robots" (VSM201) | Fair use / link | todo |
| `s4-ems-5000` | Curated | Herbie Hancock "Nobody Loves Me Like You Do" (EMS 5000 era) | Fair use / link | todo |
| `s4-vp330` | Curated | Roland VP-330 demo | Vendor demo / link | todo |

### Talk box

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-frampton` | Curated | Peter Frampton "Do You Feel Like We Do" | Fair use / link | todo |
| `s4-troutman` | Curated | Roger Troutman / Zapp "More Bounce to the Ounce" | Fair use / link | todo |
| `s4-daft-punk` | Curated | Daft Punk "Harder, Better, Faster, Stronger" | Fair use / link | todo |

### Auto-Tune as an instrument

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-cher-believe` | Curated | Cher "Believe" — pitched artifact (1998) | Fair use / link | todo |
| `s4-tpain` | Curated | T-Pain "Buy U a Drank" | Fair use / link | todo |
| `s4-travis-scott` | Curated | Travis Scott "Sicko Mode" | Fair use / link | todo |

### Modern neural SVS (what Vodechord is *not*)

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s4-synthv-demo` | Curated | Synthesizer V demo track | Vendor demo / link | todo |
| `s4-diffsinger-demo` | Curated | DiffSinger published sample | Academic / link | todo |
| `s4-vocaloid-hatsune` | Curated | Hatsune Miku reference | Fair use / link | todo |

---

## Section 5 — The Vodechord Target Sound

### Affinity profile references — Close-Mic Intimate

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-intimate-ref-1` | Curated | Billie Eilish "when the party's over" (link-out, timestamped) | Fair use / link | todo |
| `s5-intimate-ref-2` | Curated | Frank Ocean "Ivy" (link-out) | Fair use / link | todo |
| `s5-intimate-ref-3` | Curated | Phoebe Bridgers "Funeral" (link-out) | Fair use / link | todo |

### Affinity profile references — Broadcast Authority

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-authority-ref-1` | Curated | Morgan Freeman narration | Fair use / link | todo |
| `s5-authority-ref-2` | Curated | Ira Glass This American Life (podcast excerpt) | Fair use / link | todo |
| `s5-authority-ref-3` | Curated | David Attenborough nature doc | Fair use / link | todo |

### Affinity profile references — Chest-Belt Powerhouse

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-belt-ref-1` | Curated | Whitney Houston "I Will Always Love You" climax | Fair use / link | todo |
| `s5-belt-ref-2` | Curated | Adele "Rolling in the Deep" chorus | Fair use / link | todo |
| `s5-belt-ref-3` | Curated | Freddie Mercury "Somebody to Love" climax | Fair use / link | todo |

### Affinity profile references — Weaponized Grain

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-grain-ref-1` | Curated | Tom Waits "Way Down in the Hole" | Fair use / link | todo |
| `s5-grain-ref-2` | Curated | Leonard Cohen "You Want It Darker" | Fair use / link | todo |
| `s5-grain-ref-3` | Curated | Louis Armstrong "What a Wonderful World" | Fair use / link | todo |

### Affinity profile references — Clean Synthetic

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-synth-ref-1` | Curated | Kraftwerk "Computer World" | Fair use / link | todo |
| `s5-synth-ref-2` | Curated | Daft Punk "Around the World" | Fair use / link | todo |
| `s5-synth-ref-3` | Curated | Imogen Heap "Hide and Seek" | Fair use / link | todo |

### Composite listening

| ID | Type | Source | Rights | Status |
|---|---|---|---|---|
| `s5-composite-intimate` | Generated | Full-stack synthesis demonstrating Close-Mic Intimate target | MIT | todo |
| `s5-composite-belt` | Generated | Full-stack synthesis demonstrating Chest-Belt Powerhouse target | MIT | todo |

---

## Rights & Compliance Notes

**Curated recordings are never hosted on this site.** They are embedded or linked from their original sources (YouTube, artist's official channel, academic archives). This is not a rights workaround — it is a deliberate choice to respect artists and keep the project's scope narrow.

**For academic / voice-pathology references**, we link out to the original publication or research group's demonstration page. Attribution is explicit on the page where the exhibit appears.

**Fair use basis** for link-outs and short timestamp-anchored embeds relies on the commentary-and-criticism exception: the references are used to illustrate specific acoustic phenomena for educational purposes, the use is non-commercial, and the referenced works are not substituted by the exhibit.

**If an artist or label requests removal of any reference**, we comply immediately. A contact email lives on the site's About page.

**Generated examples** are MIT-licensed alongside the code that produces them. Any script that consumes third-party data (e.g., CMUDict) respects the original license; CMUDict is BSD-licensed and can be bundled.

---

## Catalog Maintenance

- Every catalog change bumps the catalog's timestamp at the bottom of this file.
- Status transitions (`todo` → `in-progress` → `done`) are tracked inline.
- A script in `/scripts/audit_catalog.py` will (eventually) cross-reference catalog IDs against actual files in `/audio/` and `/src/exhibits/` and report drift.

---

*Catalog version: April 23, 2026 — Initial catalog drawn from the Vodechord concept doc and Listening Room spec.*
