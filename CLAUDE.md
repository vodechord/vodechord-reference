# Vodechord Reference — Project Context for Claude Code

## What this project is

This repo is the **Listening Room**, a web-based ear-training site that informs design decisions for **Vodechord** — a separate, future Max for Live device that is a singing synthesizer (a performable vocal synthesizer, sequencer and visualizer that sings with your thoughts and performs with your personal touch).

**Critical distinction:** the Listening Room is NOT a demo, marketing site, or product preview for Vodechord. Vodechord the instrument does not yet exist. This site exists to ground Vodechord's eventual design in actual ear-training, by isolating the acoustic features that make synthetic voice work and letting the listener hear "good" versions of each in context.

If a feature, exhibit, or design choice doesn't help the listener hear what "good" sounds like for a specific acoustic phenomenon, it doesn't belong.

## Source of truth

The four canonical docs live in `docs/`:

- `vodechord-concept.md` — the full Vodechord instrument concept. Read this when you need to understand what the listening tool is *for*.
- `listening-app-spec.md` — the spec for this site. Sections, exhibit types, interaction model, success criteria.
- `audio-catalog.md` — every intended audio example, with ID, type (generated / curated / Web Audio), source, rights, and status.
- `architecture.md` — the technical stack, file layout, build pipeline, and BlueHost deploy.

When in doubt, defer to these. If there's a conflict between something I say in chat and what's in the docs, ask me before assuming the chat overrides the doc.

Note: the Vodechord concept doc describes confidence-state typography conventions (bold/faded/edited) for the future *instrument's* UI. Those rules are for Vodechord-the-Max-for-Live-device, not for this site. The Listening Room has its own visual identity, defined below.

## Audio sourcing rules

- **Generated** examples: MIT-licensed, produced by scripts in `/scripts/`, output to `/audio/generated/`, committed.
- **Curated** examples: never hosted on this site. Link out to original sources only. The site never embeds copyrighted audio.
- **Web Audio** exhibits: synthesized in the browser at play time. Source is JS/DSP code in `/src/exhibits/`.

## How I (the project owner) work

- Iteratively. Build one exhibit at a time. I want to listen, react, and inform the next iteration. Resist the temptation to batch big work.
- Confirm understanding before writing code on substantial tasks. A short plan first, then implement.
- Match the architecture in `docs/architecture.md`. If you'd deviate, flag it and ask.
- Commit messages should describe the actual change, not "update files." Do not add a `Co-Authored-By: Claude` trailer.
- Don't add dependencies casually. Anything with a build step or a transitive dependency tree gets a question first.

## Credits

Vodechord and the Listening Room are created by Jerome Miller with Claude Design. The prototype pages in `src/` (`my-name-is-voice.html`, `waveguide-lab.html`) are single-file bundles exported from Claude Design — replace them with a fresh export rather than hand-editing the bundled markup.

## Aesthetic direction

**Bell Labs meets Teenage Engineering OP-XY, creating a valley of the uncanny voice synth.**

Two parents. From Bell Labs: the vocal-tract cross-section, the area function, the spectrogram, the numbered figure — the voice treated as a physical system and drawn with anatomical and acoustic accuracy. From the OP-XY: a black instrument with white legends, a hard grid, monochrome display, tiny uppercase labels, nothing decorative. The result should feel like lab equipment that happens to sing. The voice is not trying to pass as a real human. It should land eerily close to real and remain definitely not — that gap is the instrument's character, not a defect to engineer away.

These rules describe what the shipped pages actually do. `src/my-name-is-voice.html` is the reference implementation; when a rule here and that page disagree, ask.

### Palette

Black, white, and a gray ramp. No brand color.

```
--bg:   #000      /* page and panel background */
--g1:   #0a0a0a   /* raised surface */
--g2:   #141414   /* section header bars, hover */
--g3:   #2a2a2a   /* rules, cell dividers (also --rule) */
--g4:   #4a4a4a   /* inactive tracks, idle indicators */
--g5:   #8a8a8a   /* secondary labels, hints */
--g6:   #c8c8c8   /* primary labels */
--fg:   #fff      /* values, active state, legends */
```

Waveguide Lab uses the same ramp a hair warmer (`#0a0a0a` ground, `#f2f2f0` foreground); either is fine, don't mix them on one page.

### Use of color

- **State is shown with white, not hue.** Active = white text, white 1px inset outline, or a full invert (white fill, black text). Inactive = gray.
- Color appears only as a functional signal, and only where Mr Voice already uses it: amber `oklch(0.82 0.16 80)` for tuning/dev controls and warnings, red `oklch(0.7 0.18 25)` for errors, red-orange `#d8543b` / `#ff6a4d` for the one armed/precision toggle. Ask before adding another.
- No decorative color, no tinted backgrounds, no colored series in visualizations — distinguish traces by weight, dash, and gray level.

### Typography

- **JetBrains Mono** — the voice of the instrument. Readouts, values, phonemes, body UI text (13px base, slight positive tracking). If in doubt, it's mono.
- **Archivo** — panel titles and parameter labels, as in Waveguide Lab: uppercase, weight 500–700, 9–13px, letter-spacing 0.14–0.2em.
- Labels are small, uppercase and widely tracked (0.12–0.24em). Values are larger and brighter than their labels.
- The index page is a plain neutral system-sans list; keep it that quiet.
- No serif display type, no editorial headline styling.

### Layout and controls

- **Hard grid.** Panels are cells divided by 1px `--rule` lines (grid with 1px gaps over a rule-colored background). Full-bleed bands, no cards, no floating panels, no outer margins beyond a thin frame.
- **Square corners.** Radius 0 everywhere; 1–2px only on slider thumbs and LED chips; 50% only for indicator dots.
- **Sliders** are a 1–2px track with a small rectangular white thumb or a solid white fill bar. **Buttons** are a 1px outline with uppercase tracked mono text; hover fills gray, active inverts.
- **Dense.** Tight padding (6–12px), many parameters visible at once, every one labelled and showing its numeric value.
- Glyphs and symbols over icons: `◌ ♪ ↺ ↕ ‖ ·` and IPA, set in the type itself.

### Diagrams and visualizers

- White and gray line work on black. The tract, waveform, spectrogram and automation lanes are the only "illustration" the site has, and they are drawn from the live model state, not decoratively.
- Line weight carries hierarchy: the thing that is sounding is white and heavier; reference and what-will-play lines are gray and thin.
- Label axes with real units (Hz, ms, semitones, note names) in mono. No decorative captions, scale marks or figure labels that don't carry information.

### What to avoid

- Drop shadows and soft glows. The single exception is the small white bloom on a live MIDI/RX indicator.
- Gradients, beyond a near-invisible `#050505` → `#000` on a band.
- Rounded cards, glassmorphism, neumorphism, skeuomorphic knobs, VU glass, wood or metal textures.
- Warm paper/cream surfaces, serif editorial layouts, illustrated anatomy.
- Color used for brand or mood. Emoji.
- Chasing human realism, or presenting the voice as a person: no mascots, faces, or copy that implies a real singer. Eerily close to real, definitely not — keep everyone aware of that.

### The look-and-feel test

Does it look like a panel on a piece of black lab hardware that Bell Labs and Teenage Engineering built together — every mark a control, a measurement, or a legend? Or does it look like a website about a synth? Aim for the former.

## Build waves (current plan)

We're building in waves rather than all at once:

1. **Scaffold** — index, navigation, one section page. No exhibits.
2. **Vowel space explorer** — Section 2's hero exhibit. Foundational DSP work (LF glottal pulse + formant filter) lands here.
3. **Section 1 (Source) exhibits** — pitch, jitter, shimmer, HNR, tilt, grit.
4. **Section 2 (Filter) remaining** — vowels (you've done the explorer), nasals, fricatives, transitions, presence.
5. **Section 3 (Expression)** — vibrato, portamento, microtiming, energy, breath.
6. **Sections 4 & 5 (Lineage and Target)** — mostly curated, content-heavy.
7. **Polish** — landing page design pass, accessibility, mobile, copy.

Don't skip ahead. Each wave's output informs the next.

## Deploy

Push to `main` triggers GitHub Actions, which runs lint, link-check, build, and deploys via rsync to `/home1/wmvyeomy/public_html/website_0cf35245/listening-room/` on BlueHost. The site is live at https://vodechord.com/listening-room/.

## What to do at the start of a session

1. Read this file (which you're doing).
2. If the task touches an exhibit or the spec, also skim the relevant section of `docs/listening-app-spec.md`.
3. If the task touches build, deploy, or architecture, also skim `docs/architecture.md`.
4. Propose a plan for the task before writing code. Wait for confirmation before implementing.
5. After implementation, summarize what changed and how to verify it works.
