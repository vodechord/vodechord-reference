# Vodechord Reference — Listening Room

A web-based ear-training companion to the [Vodechord](./docs/vodechord-concept.md) project.

The Listening Room collects curated and generated audio examples of the acoustic techniques that make Vodechord's synthesized voice work — jitter, shimmer, spectral tilt, the singer's formant, grit modes, vibrato, microtiming, breath, and more. Each example isolates a single feature so you can hear what "good" sounds like for that dimension, then generalize from that ear training into design decisions for the Vodechord instrument itself.

**Live site:** _to be deployed at a subdirectory of the project's BlueHost server (URL TBD)_
**Status:** pre-alpha — docs and scaffolding in place; audio exhibits still to be built.

---

## Why this exists

Vodechord is a playable vocal instrument (a "singing synthesizer") in development as a Max for Live device. Before committing to engineering decisions about which acoustic features the instrument should model, expose as controls, or leave fixed, the project needs a grounded understanding of how each feature *sounds* in isolation and in context.

The Listening Room is where that grounding happens. It is not a textbook, a product demo, or a sales page. It is a sound laboratory.

Every exhibit answers one question: **Does this help me hear what "good" sounds like for this feature?** If it doesn't, it doesn't belong.

---

## Repo layout

```
vodechord-reference/
├── README.md                      ← you are here
├── LICENSE                        ← MIT
├── CONTRIBUTING.md
├── docs/
│   ├── vodechord-concept.md       ← the full Vodechord instrument concept
│   ├── listening-app-spec.md      ← this site's behavior and sections
│   ├── audio-catalog.md           ← every intended audio example, indexed
│   └── architecture.md            ← web stack, Web Audio strategy, deploy
├── src/                           ← site source (HTML / CSS / JS / Web Audio)
├── audio/                         ← static audio assets (committed or CDN'd)
├── scripts/                       ← audio generation scripts (Python / sox / ffmpeg)
└── .github/workflows/
    └── ci.yml                     ← lint, test, deploy to BlueHost on main
```

---

## Relationship to Vodechord

The Listening Room is a **companion artifact**, not the Vodechord instrument itself. The instrument lives in a separate repo (not yet created) and is a Max for Live device. This site exists to inform that device's design.

Where the two projects overlap:

- Some of the generation scripts in `scripts/` are early prototypes of DSP techniques Vodechord will use. These may eventually move into the instrument's codebase.
- The concept doc (`docs/vodechord-concept.md`) is the canonical source for the Vodechord design and is kept current here for convenience. If Vodechord ever gets its own repo, the concept doc will move there and this site will reference it.

---

## Getting started

Until the site scaffolding is written, there's nothing to run. Check back after the first commits land in `src/`. A local dev workflow (likely `npx serve` or similar) will be documented here once it exists.

---

## Contributing

This project is public and open to contribution, but is primarily personal at the moment. See [CONTRIBUTING.md](./CONTRIBUTING.md) for expectations.

---

## License

MIT. See [LICENSE](./LICENSE).

Audio examples bundled in this site are covered by the license of their original source where applicable; generated examples are MIT alongside the code that produces them. The audio catalog ([docs/audio-catalog.md](./docs/audio-catalog.md)) documents sourcing and rights per example.
