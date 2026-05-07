# Design Brief: Vowel Space Explorer
## Vodechord Listening Room — Section 2 Hero Exhibit

---

## Project context

The Listening Room is a web-based ear-training site for musicians, producers, and voice-curious listeners. It teaches the acoustic features of the human voice through carefully isolated, comparative listening exhibits. It is the companion artifact to **Vodechord**, a future singing-synthesizer instrument; the Listening Room exists to ground Vodechord's design in real listening rather than abstract concept-doc language.

This particular exhibit — the **Vowel Space Explorer** — is the visual hero of the site's second section ("The Filter"). It's where the listener learns that vowels are coordinates in a frequency space defined by the first two formants (F1 and F2), and that everything else about voice (consonants, pitch, breathiness, character) is layered on top of this foundation.

## What the exhibit does

A user drags a 2D cursor on an F1/F2 plane and hears synthesized vowels morph in real time. Simultaneously, an animated medial cross-section of a human head shows the vocal tract reconfiguring — tongue position, jaw opening, lip rounding — to match the vowel currently being produced. F1 (pharyngeal cavity resonance) and F2 (oral cavity resonance) are visualized as gently energized regions of the vocal tract that bloom faintly in amber as they resonate.

The cross-section is the visual hero. The interaction (the F1/F2 plane) is essential but visually subordinate.

## Aesthetic direction (firm — single direction, please don't propose alternates)

The visual identity is **midcentury linguistics-textbook clarity meets editorial publication warmth.** Imagine a cross-section diagram from a 1950s Bell Labs research monograph — confident hand-drafted line work, anatomical precision, restrained palette — reproduced in a thoughtful contemporary publication. Slightly digital, never cartoonish.

Reference points: scientific monograph plates, IPA chart illustrations, Daniel Jones-style phonetics diagrams, engineering schematics. Drawn with care and warmth, but unmistakably technical figures, not editorial illustration.

This is **not** soft editorial illustration. **Not** photorealistic anatomy. **Not** stock-illustration character work. **Not** glowing sci-fi medical UI.

### Required style attributes

- **Confident technical line drawing** as the primary visual technique. Hand-drafted feel, slightly digital. Linework over fill. Crisp strokes, no painterly textures.
- **Anatomical accuracy** of the vocal tract (tongue, jaw, lips, soft palate, larynx, pharynx, hard palate). The shape and relative positions of these structures should be recognizably correct to a phonetician.
- **Hierarchy of detail.** The vocal tract — tongue, jaw, lips, soft palate, larynx — is the main event. Render it with primary line weight and full detail. The rest of the head (skull, brain, neck, spine, hair, facial features beyond the mouth) is present but stylized down to ghostly outlines or dotted strokes. The viewer's eye should land on the articulators immediately.
- **Negative space is structural.** Don't crowd the figure with decoration. The cross-section should breathe.

### Palette (use exactly these tokens)

```
--ink:        #16110f   /* primary background — warm near-black */
--ink-2:      #1f1a17   /* elevated surfaces, panel backgrounds */
--cream:      #f5efe3   /* primary foreground text on dark surfaces */
--paper:      #e8dfc8   /* cream paper for any editorial content blocks */
--muted:      #8a7f72   /* muted UI text */
--rule:       #3a302b   /* hairline dividers */
--diagram:    #b8a896   /* default diagram line color on dark backgrounds */
--amber:      #ff9a3c   /* primary accent — active resonance, current state */
--amber-glow: #ff9a3c66 /* subtle bloom for active elements */
--teal:       #5fb3a5   /* asynchronous / running process */
```

The page background is `--ink`. The cross-section diagram lives on a slightly elevated panel of `--ink-2` so it's distinct from the page chrome. Diagram line work is `--diagram` for default detail and `--cream` for emphasis on the active articulator. Amber is reserved for *what's currently resonating* — F1 region for one vowel, F2 region for another — used sparingly. Never bright white. Never RGB.

### Typography (use exactly these)

- **Fraunces** (variable serif) for any prose, headings, IPA vowel symbols (set large and elegantly).
- **Instrument Sans** for UI labels and chrome — small caps or all-caps, generous letter-spacing.
- **JetBrains Mono** for numeric readouts: F1 frequency, F2 frequency, both in Hz.
- Off-limits: Inter, Space Grotesk, generic system stacks, anything that reads as default AI tech site.

## Layout

Desktop layout, roughly 1200px wide:

- **Header bar** (slim, ~40px tall, `--ink`): site mark "Listening Room" set in Fraunces small. To the right, breadcrumb in Instrument Sans: "Section 2 · The Filter · Vowel Space Explorer."
- **Main content area** divided roughly 60/40 horizontally:
  - **Left (60%):** the cross-section diagram, occupying a tall panel of `--ink-2`. The diagram itself is centered and breathing, perhaps 70% the panel's height. Above the panel, in Fraunces: a one-line title like "The Vocal Tract." Below the panel, optional small annotations in Instrument Sans pointing to anatomical landmarks (tongue, soft palate, lips, larynx) with thin leader lines in `--rule`.
  - **Right (40%):** the F1/F2 interactive plane. A square or near-square plot, axes labeled F1 (vertical, increasing downward, ~200–800 Hz) and F2 (horizontal, increasing leftward, ~600–2500 Hz — the standard linguist's orientation). IPA vowel symbols positioned at canonical F1/F2 coordinates as faint reference markers in `--muted`. The user's cursor is a small amber crosshair. Below the plane, a tight monospace readout of current values:
    ```
    F1   730 Hz
    F2  1090 Hz
    /ɑ/  open back unrounded
    ```
  - At the very bottom of the right column, a small Web Audio control: a play/pause button in Instrument Sans style and a "loop" toggle. Sound plays continuously while the user drags; this control is for explicit start/stop.
- **Below the main content** (full width): three or four short paragraphs of editorial prose in Fraunces, set on `--paper` cream blocks, explaining what the listener is hearing and why it matters. This is where the editorial-publication feel earns its keep — the prose is set with care, and the cream blocks are visually distinct from the dark technical diagram above.

## Animation behavior

For this design pass, three static frames at three different vowel positions are sufficient to communicate the intent:

1. **/i/ (ee)** — tongue arched high and forward, jaw slightly closed, lips spread/neutral. F2 region (oral cavity, front) glowing faintly amber.
2. **/ɑ/ (ah)** — tongue low and back, jaw open wide, lips neutral. F1 region (pharyngeal cavity) glowing faintly amber.
3. **/u/ (oo)** — tongue high and back, jaw slightly closed, lips rounded forward. Both F1 and F2 regions present at lower amplitude, no strong amber dominance.

Show me these as three side-by-side frames so I can evaluate the animation logic. The actual interpolation between them will be implemented in code; what I need from you is the static target poses.

## What I want from you

A single, fully-resolved visual direction. Not three options to choose from — commit to the one direction described above and execute it with care.

Specifically:

1. The cross-section illustration as a static SVG-style technical diagram, in three vowel poses (/i/, /ɑ/, /u/) for review.
2. The full exhibit page layout (header, diagram panel, F1/F2 interactive plane, readouts, editorial prose blocks below) as a static mockup at desktop breakpoint.
3. One detail callout showing how the active resonance regions glow in amber — what "subtle amber bloom on the F1 cavity" actually looks like in this diagram style.

If anything in the brief feels under-specified, ask before generating. I'd rather refine the brief than generate against ambiguity and have to redo it.

## What success looks like

The final output should make a phonetician say "that's a real vocal tract diagram" and an editorial designer say "that page is set with care." Anything that reads as generic AI illustration, stock anatomy, or tech-startup UI has missed the brief.
