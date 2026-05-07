# Vodechord Reference — Project Context for Claude Code

## What this project is

This repo is the **Listening Room**, a web-based ear-training site that informs design decisions for **Vodechord** — a separate, future Max for Live device that is a singing synthesizer (a playable vocal instrument that sings what you type).

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

## Stack constraints (firm)

- Vanilla HTML, CSS, JS. No frameworks. No Tailwind. No build step beyond optional concatenation/minification.
- Web Audio API for real-time interactive exhibits.
- Python (numpy, scipy, librosa, soundfile, pyworld) in `/scripts/` for pre-rendered audio generation. Deterministic with fixed seeds.
- BlueHost shared hosting. Deploys via GitHub Actions + rsync over SSH on push to main.
- Production URL: https://vodechord.com/listening-room/

If a task seems to want a framework or a build pipeline more elaborate than the above, push back rather than introducing one.

## Audio sourcing rules

- **Generated** examples: MIT-licensed, produced by scripts in `/scripts/`, output to `/audio/generated/`, committed.
- **Curated** examples: never hosted on this site. Link out to original sources only. The site never embeds copyrighted audio.
- **Web Audio** exhibits: synthesized in the browser at play time. Source is JS/DSP code in `/src/exhibits/`.

## How I (the project owner) work

- Iteratively. Build one exhibit at a time. I want to listen, react, and inform the next iteration. Resist the temptation to batch big work.
- Confirm understanding before writing code on substantial tasks. A short plan first, then implement.
- Match the architecture in `docs/architecture.md`. If you'd deviate, flag it and ask.
- Commit messages should describe the actual change, not "update files."
- Don't add dependencies casually. Anything with a build step or a transitive dependency tree gets a question first.

## Aesthetic direction

The Listening Room visual identity is: **midcentury linguistics-textbook clarity meets editorial publication warmth.** Imagine the cross-section diagrams, IPA charts, and acoustic spectrograms you'd find in a Bell Labs research monograph from the 1950s — confident hand-drafted line work, anatomical precision, restrained palette — reproduced in a thoughtful contemporary publication.

Key principles:

- **Illustrations are technical diagrams, not soft editorial illustrations.** Clean line work, confident strokes, anatomical and acoustic accuracy over decorative flourish. The look of a precise hand-drafted figure, slightly digital but never cartoonish.
- **Restraint over abundance.** Most surfaces are quiet. A typical page has a single illustration, a tightly set headline, paragraphs of editorial prose, an interactive control, and almost nothing else. Negative space is doing as much work as the marks.
- **Type carries the warmth.** Where the diagrams are precise and reserved, the typography is alive — Fraunces with its optical-size and softness axes, set with care.
- **Active areas are exaggerated; everything else recedes.** When an exhibit focuses on a specific anatomical or acoustic region, that region is rendered with more detail, line weight, and contrast. Surrounding context is present but stylized down to ghostly outlines.

### Typography

- **Fraunces** (variable serif, opsz and SOFT axes) — lyrics, headings, editorial body copy. The hero typeface.
- **Instrument Sans** — UI labels, chrome, navigation, button text.
- **JetBrains Mono** — numeric readouts, frequency values in Hz, code, tabular data.
- **Off-limits:** Space Grotesk, Inter, generic system stacks, anything that reads as "default AI tech site."

### Palette

```
--ink:        #16110f   /* primary background — warm near-black */
--ink-2:      #1f1a17   /* elevated surfaces, panel backgrounds */
--ink-3:      #2a2320   /* insets, wells, nested surfaces */
--paper:      #e8dfc8   /* cream paper for editorial content blocks */
--paper-dim:  #c9bfa6   /* dim paper for secondary content */
--cream:      #f5efe3   /* primary foreground text on dark surfaces */
--muted:      #8a7f72   /* muted UI text */
--muted-2:    #6a6158   /* deeply muted, near-rule-color text */
--rule:       #3a302b   /* hairline dividers and frame strokes */
--ink-line:   #2a221d   /* subtle internal line work on diagrams */
--diagram:    #b8a896   /* diagram line color on dark backgrounds */
--amber:      #ff9a3c   /* primary accent — tube glow, active states */
--amber-dim:  #c97a2f   /* hover/pressed amber */
--amber-glow: #ff9a3c66 /* subtle bloom effect for active elements */
--teal:       #5fb3a5   /* secondary state — phosphor, automation, running processes */
--rose:       #e06c5a   /* edits, overrides, user-modified content */
```

### Use of color

- Ink (background) and cream/paper (foreground) do most of the work. The site is fundamentally a "warm-dark" theme with cream editorial blocks for prose-heavy sections.
- Diagram line work uses `--diagram` (a desaturated warm gray) as default and `--cream` for emphasis. Never use bright white.
- Amber is *scarce*. It marks active states, the currently-playing note, the resonance band that's energized, the value the user just adjusted. If amber is on more than two things on screen, it's wrong.
- Teal signals automation or asynchronous processes — buffering, "running," background jobs.
- Rose marks user overrides — a value the user has manually set, an edited label, a custom curve.
- Decorative color is suspicious. Ask before adding more colors than the palette above.

### Diagram conventions

- **Line weight hierarchy:** primary subject in confident strokes (1.5–2.5px equivalent). Secondary detail in thinner strokes (0.75–1px). Ghosted context in dotted or very thin solid lines.
- **Active resonance / focus regions** glow gently in amber — a soft fill at low opacity, perhaps a slightly thicker outline. Never a hard glow or RGB-style bloom.
- **Annotations** use Instrument Sans for labels (small caps or all-caps, generous letter-spacing), with thin leader lines in `--rule`.
- **Frequency / value readouts** in JetBrains Mono.
- **Background of diagram surfaces** is `--ink-2` with a barely-visible grid or rule structure if grid is needed at all.

### What to avoid

- Drop shadows. Box shadows. Glow effects beyond the gentle amber bloom on active states.
- Gradients except very subtle ones (e.g., `--ink` to `--ink-2` for depth).
- Glassmorphism, neumorphism, beveled controls.
- Faux-analog VU glass, faux-knurled metal knobs, faux-wood paneling.
- Generic anatomy stock illustration. Cartoon-y line art with disproportions.
- RGB / cyan / magenta / purple. Tech-startup brand chrome.
- Emoji as visual elements.
- Rounded corners on frames or diagram panels — keep edges crisp. Sliders and small interactive controls can have small (2px) radius for tactility.

### The look-and-feel test

When generating new visuals, the test is: does this look like a careful figure in an academic acoustics monograph or a thoughtful music-tech editorial publication? Or does it look like generic AI startup output? Aim for the former, always.

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
