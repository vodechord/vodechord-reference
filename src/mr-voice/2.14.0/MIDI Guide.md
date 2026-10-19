# VTM·02 — MIDI guide

*Updated for 2.14.0 (test release).* Phrase markup and editor gestures: **Phrase Guide.html** (GUIDE ↗ next to PHRASE).

## 1. Quickest test (one cable, no extra software)

1. Plug the controller into the Mac over USB. The microKorg XL, OP‑XY, Push 2 and KeyStep Pro all work without drivers.
2. Open the app in Chrome, click **ENGAGE**, then **MIDI → CONNECT**, and choose **Allow**.
3. Play. The RX LED blinks with each message, and **SETUP → MON** lists exactly what arrives.

Ableton can stay open, because macOS shares MIDI ports between apps. Disarm the Live track if you don't want both playing.

**Use wired audio for playing.** Bluetooth headphones and earbuds add about 300 ms of output delay (measured on Pixel Buds), which makes the voice unplayable as an instrument. Use the Mac's speakers or wired headphones. To check, press **Shift+L** for the latency panel: **out** should be well under 50 ms.

## 2. The SETUP panel

The **IN** and **CH** menus are always shown at the top. They choose the one input and channel the voice listens to, or **ALL**. Everything else is saved in the browser and survives reloads.

### BIND — map controls to parameters

Each parameter row has **LEARN** (move a control to bind it) and **×** (remove the binding).

Per‑binding options:

| Option | What it does |
|---|---|
| **ABS** | Normal knobs and faders |
| **REL 2C** | Push 2, and most endless encoders |
| **REL BIN** | Endless encoders where 64 is the centre |
| **REL SGN** | Endless encoders where bit 6 is the sign |
| **PICK** | Soft takeover: the control is ignored until it reaches the on‑screen value. ▲ or ▼ shows which way to turn. |
| **LIN / EXP / LOG** | Response curve (ABS only) |
| **INV** | Reverses the direction |

**High‑resolution controls** (for smooth F0 and LUNG):
- **14‑bit CC** is detected automatically. When a controller sends CC *n* followed by CC *n*+32, the row shows a **14** badge.
- **NRPN** controls can be LEARNed like any CC.
- RPN 0 (pitch bend range sent from the controller) is honoured.

**Profiles:**
- Bindings are saved **per input device**. When you select a device in IN, its own map loads.
- **GENERIC** loads the default map below.
- **PUSH 2** sets up the relative encoders and phoneme pads (see §4).
- **EXPORT / IMPORT** saves or loads a controller map as a JSON file.

**Default map:**

| CC | Parameter |
|---|---|
| 1 | VOWEL ɑ→i→u |
| 2 | BREATH |
| 7 | GAIN |
| 11 | LUNG |
| 70 | FRICATION |
| 71 | TENSION |
| 72 | ADDUCT |
| 73 | LIP |
| 74 | TONGUE ↔ |
| 75 | TONGUE ↕ |
| 76 | VELUM |
| 77 | VIBRATO |
| 78 | JAW |
| 79 | LARYNX |

F0 can also be bound; it uses a log scale.

**Changed from before:** the mod wheel (CC1) now sweeps one axis through the vowels ɑ → i → u. Vibrato moved to CC77.

**Phrase parameters:** **KEY ROOT** (C…B across the CC range) is bindable like any other row.
**VOICE SIZE:** bindable, 11–20 cm across the CC range. The tube changes in whole sections (≈0.36 cm at 48 kHz), so a sweep moves in small audible steps; it resamples rather than restarts, so it's safe mid‑note.

### PLAY — how notes behave

- **Note priority:** LAST, LOW or HIGH, using a held‑note stack. Releasing the top note always slurs back to the note you're still holding.
- **Overlap:** LEGATO glides the pitch without re‑closing the folds (sung). RE‑ARTICULATE gives each new note a short fold opening and pressure dip.
- **Bend range:** ±1–12 semitones.
- **Velocity → LUNG:** each note breathes at 0.55–1.00 × the LUNG knob, depending on velocity. The knob itself never moves, and GAIN is left alone.
- **Aftertouch:** routes to BREATH, LUNG (0.6–1.2 × the note's pressure), VIBRATO, or OFF. Poly aftertouch follows the most recent note.
- **Sustain (CC64):** holds the voice after you let go of the keys.
- **Program change:** 1–4 select LIGHT MIX, OPERA, FORWARD and ROCK.

### Notes while a phrase plays

- **A held note overrides the phrase pitch.** The voice glides from the current F0 to the note in about 35 ms (about 25 ms between notes).
- **Releasing the note** glides back to the phrase's programmed contour in about 70 ms, and the phrase carries on. Releasing never stops the phrase.
- **Articulation and breath** stay with the phrase while it plays. Only pitch responds to the keys.
- **LUNG** (knob, CC11 or aftertouch) changes the breath of a phrase while it's playing.
- **The F0 lane** in the phrase editor draws what was actually played as a white line, so you can see your held notes against the programmed contour.

### KEYS — phoneme zone

- Keys from **ZONE LOW** up to **SPLIT** latch phonemes, laid out as vowels, then sonorants, fricatives and stops. The map is shown in the panel.
- Keys outside the zone play pitch.
- The default is C2–B3 (24 keys): all 13 vowels plus the sonorants and /f v θ ð/.
- **One hand:** a phoneme key held on its own sounds at the current F0, just like clicking the phoneme bank.
- **Both hands:** hold a vowel with one hand and play the melody with the other. Tapping a consonant while the melody is held articulates it, and releasing it returns to the last vowel.

### PHRASE PADS — trigger phrases from pads or keys

The phrase area has a grid of pads, set to **4×1, 4×2 (default), 4×4 or 8×4**. Pad 1 is bottom‑left, as on MPC and Push‑style controllers.

- **MIDI PADS ON** maps notes to pads, starting at **FROM** (default C2 / 36). With 4×2 that's C2–G2 (notes 36–43). The note names show on the pads while it's on.
- **Pads win over the phoneme zone.** Notes in the pad range trigger phrases and are not used as phoneme keys or pitch.
- **Pads are one‑shot.** A pad starts its phrase, and releasing it does nothing. Hitting a pad while a phrase plays restarts the phrase from its first phoneme.
- **It's off by default**, so a keyboard's low notes play pitch until you turn it on.
- **Avoiding overlaps:** move either FROM or the phoneme ZONE so the two ranges don't clash. For example, put pads on C1 (24) and keep phonemes at C2–B3.
- **Default pad set (4×2):**

| Pad | Note | Phrase | Shows |
|---|---|---|---|
| 1 | C2 | Hello world. | plain speech |
| 2 | C♯2 | Are you there? | rising question |
| 3 | D2 | Wait, let me think… okay! | rests |
| 4 | D♯2 | I *really* mean it. | stress |
| 5 | E2 | Go ^up, then _down. | pitch steps |
| 6 | F2 | Sing it~ with me~. | vibrato |
| 7 | F♯2 | Stop. That is LOUD! | tense voice |
| 8 | G2 | Say /h ɛ l oʊ/ — then breathe; done. | inline IPA |

To change a pad, type or load a phrase, click **ASSIGN**, then click the pad. Right‑click a pad to clear it. **↺** restores the defaults.

### SYNC — clock and MIDI out

**Clock input**
- **CLOCK FROM:** OFF, ANY input, or a specific port. This is separate from IN, so notes can come from the KeyStep while clock comes from Live.
- **START / STOP:** runs SAY IT and STOP.
- **RE‑SAY EVERY:** restarts the phrase every 1, 2 or 4 bars.
- **TEMPO → RATE:** 120 BPM = 1.00×.

**MIDI OUT**
- **VALUE ECHO** sends every bound value back as a CC, so LED rings follow the screen. It's paused for 250 ms after you touch a control, so it never fights your hand.
- **PAD LEDS → PUSH 2** lights the phoneme pads in greys, with the active phoneme in white. Phrase pads are not lit yet.

### KEY — scale constraint
MIDI has no live message for key or scale, and Live and the OP‑XY don't send theirs, so VTM learns it:
- **KEY** on in the timeline header, pick root and scale, or press **LEARN** and play the scale or tonic chord from any input. First note = root; it sets 1.5 s after the last note.
- Phrase notes snap to the nearest in‑key note; an exact in‑between follows the melody direction. Held MIDI notes are never changed.
- Bind **KEY ROOT** to a knob to change key mid‑song.

### MON

Shows the last 16 messages that passed the IN and CH filter, plus clock start and stop.

## 3. Your controllers

### microKorg XL
- **Setup:** set IN to the XL and CH to 1. The keys, pitch wheel and mod wheel work straight away; the mod wheel does the vowel sweep.
- **Knobs:** LEARN its two knobs onto TONGUE ↔ and TONGUE ↕. They send absolute values, so turn **PICK** on.
- **Phoneme keys:** with only 37 keys, set ZONE LOW = C2 and SPLIT = C3 for 12 vowel keys, or use octave shift to reach the zone.

### OP‑XY
- **Connection:** USB‑C or Bluetooth MIDI. Bluetooth *MIDI* is fine for control; it's Bluetooth *audio* that adds the delay.
- **Simplest:** use **COM → MIDI controller mode**, and set its channel with shift + the dark‑grey knob.
- **Sequenced:** on the external MIDI track, M2/M3 give 8 CCs. Set them to the default numbers above (shift + turn an encoder).
- **Clock:** its sequencer can drive **RE‑SAY EVERY**.
- **Encoders:** if a knob jumps, switch that binding to REL 2C.

### Push 2
1. In Live, press **USER** on the Push (or quit Live).
2. Set IN = *Ableton Push 2 User Port*.
3. On the BIND tab, click **PUSH 2**. This binds the eight track encoders plus the master encoder as REL 2C, turns on the phoneme pads (bottom 37 pads), and routes aftertouch to LUNG.
   - To trigger phrases from Push 2, turn on **MIDI PADS** and set FROM above the phoneme pads (e.g. 36 + 37 = 73), or turn the phoneme zone off.
4. Set MIDI OUT to the User Port so the pads light up. The pad colours use Push 2's default palette (indices 122–124); if they look wrong, turn PAD LEDS off.

### KeyStep Pro
- **Setup:** set CH to the track you're playing, which isolates it from the other three.
- **Knobs:** set them to the default CCs in Arturia MIDI Control Center, or LEARN them.
- **Aftertouch:** set it to **LUNG** for crescendos on a held note.

## 4. Driving VTM from Ableton

1. In **Audio MIDI Setup → IAC Driver**, tick "Device is online".
2. In Live, set a MIDI track's output to IAC Bus 1.
3. In VTM, set CLOCK FROM = IAC and turn on START / STOP. Live's transport now triggers phrases, and CC automation lanes move the articulators.

## 5. Not MIDI‑controllable (yet)
- **Phrase markup** (`,` `*stress*` `^` `_` `~` CAPS `+` `|`) is typed into the Phrase field, not sent over MIDI. See Phrase Guide.html.
- **Key / scale** can't be received or sent over MIDI (no such message); use KEY › LEARN.

## 6. Not done yet
- **MPE:** not supported yet.
- **Push 2 display:** the screen can't be reached from a browser.
- **SysEx dumps:** not supported yet.
- **Push 2 phrase‑pad LEDs:** not lit yet.
