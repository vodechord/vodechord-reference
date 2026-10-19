# Mr Voice VTM — Release notes 2.9.0 → 2.14.0

## 2.14.0 — 2026-10-18 · Test release
**In the package:** `Mr Voice VTM2.14.0 (standalone).html` (open in Chrome, offline‑capable) · Phrase Guide · Workflows · MIDI Guide · these notes.

**What to test**
1. Breath and noise: /h/, breathy vowels, fricatives. The 2.10.1 noise fix should remove the cyclical, digital texture.
2. Word joins: `Are+you there?` vs `Go | up. Go up. Go+up.` — links should flow, breaks should separate.
3. Live editing: ⟲ loop a pad, press and drag F0 bars, ⇧/⌘ groups, then ASSIGN to a pad and recall it.
4. Styles mid‑phrase: switch LIGHT MIX / OPERA / FORWARD / ROCK while looping.
5. KEY: LEARN from a sequencer, sing a phrase, check ties follow the melody.
6. MIDI: notes C0…C8, VOICE SIZE and KEY ROOT binds, clock start → loop.

**Changed**
- ARC lane removed for now (the editor and its ARC START / END binds). Speech arc and sing melody stay at their defaults. To be revisited.
- Header title reads 2.14.

**Report:** what you played, phrase text, browser, audio route (wired / Bluetooth), and the SHIFT+L latency TOTAL if timing felt off.

## 2.13.2 — 2026-10-18 · Jerome's notes
- VOICE SIZE is MIDI‑bindable (SETUP › BIND, 11–20 cm).
- Fig A: removed the duplicate cm readout from the vertebra label (the value lives next to the VOICE SIZE slider).

## 2.13.1 — 2026-10-18 · Phrase Guide
- **Phrase Guide** (GUIDE ↗ next to PHRASE): all markup, speak vs sing rules, word joins, key, editor gestures, recipes.
- MIDI Guide and Workflows updated to 2.13.

## 2.13.0 · Voice size in the anatomy
- Fig A: VOICE SIZE moves the larynx along the spine (CHILD ≈ C4, F ≈ C5, M/L ≈ C6). Vertebrae labelled C1–C6, the larynx level outlined with the tract length (`C6 · 16.0 cm`).
- 1 cm ruler ticks along the airway (5 cm longer); vocal folds scale with size. C‑SPINE label fixed.
- Timeline header wraps instead of overflowing; readouts truncate.

## 2.12.1 · Styles mid‑phrase
- LIGHT MIX / OPERA / FORWARD / ROCK now take effect immediately during a playing or looping phrase and hold for every following phoneme (larynx glides ~60 ms; jaw, lip and tongue bias apply to vowels, closures stay sealed).

## 2.12.0 · Arc lane & key constraint
- ~~ARC lane~~ (removed in 2.14.0): speech declination start/end and sing melody slots.
- **KEY**: root, 8 scales, sing‑only or speech too, LEARN from MIDI. Nearest in‑key note; ties follow melody direction. KEY ROOT bindable. Held MIDI notes untouched.

## 2.11.0 · Cleaner noise & new example phrases
- Removed three legacy noise patches (smoothed aspiration drive, aspiration low‑pass, heavy trachea loss) — no longer needed after 2.10.1.
- New pad set demonstrating every mark: `Are+you there?` · `Well, I think; maybe — not.` · `… and *now* we begin!` · `Climb ^^up ^high, then _fall _down.` · `Hold on~ for me~…` · `NO. I said NO!` · `Say /h ɛ l oʊ/, not hello.` · `Go | up. Go up. Go+up.`

## 2.10.2 · A/B toggles for legacy patches (removed in 2.11.0)

## 2.10.1 · Noise generator fix
- The white‑noise generator collapsed into a 10,466‑value loop (double‑precision overflow), so every breath, frication and jitter source repeated every ~55–110 ms. Replaced with mulberry32 (period 2³²). Breathy sounds lose the "digital, cyclical" texture.

## 2.10.0 · Pitch range & editor safety
- F0 range C0 (16.35 Hz) … C8 (4186 Hz). F0 knob and DEV slider are per‑octave (log).
- F0 group drags stop as a block at the limits.
- Rests never burst a stop; final voiced‑stop release is a quieter, mostly unvoiced puff.
- Reset lengths / reset F0 need two clicks; ↺ F0 sits next to the F0 lane button; "—" lane button rightmost; the axis "0" reads "0s".

## 2.9.x · Live phrase editing
- **⟲ LOOP**; edits are heard on the running loop. F0 bars are momentary: press to hear, drag to step, release to resume.
- Readout above the lane (shows on press). F0 groups: ⇧‑click range, ⌘/Ctrl‑click add, ⌥ fine.
- Pads store F0 + timing edits (EDIT tag).
- **Word joins**: spaces link by default; vowel→vowel dip; `+` tie, `|` break. Rests pre‑shape the next sound; leading rest = silent count‑in; softer onset after rests.

## Known gaps
- ARC lane to be redesigned and restored.
- Push 2 lights for phrase pads.
- Intelligibility Bench still on older playback rules (no joins, markup prosody, ARC or KEY).
- Pad hits aren't bar‑quantised; no single voice arbiter between phrase, bank and MIDI.
- Phrase export/import.
