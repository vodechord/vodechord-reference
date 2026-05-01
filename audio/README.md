# Audio

This directory holds audio assets for the Listening Room.

## Structure

- `generated/` — WAV files produced by scripts in `/scripts/`. Committed as build artifacts so the site can be deployed without re-running audio generation. Regenerate locally with `scripts/render-audio.sh` when a generation script changes.

## What this directory does not hold

- **Curated reference recordings.** The site never hosts third-party audio. Curated references are embedded or linked to their original sources (YouTube, artist pages, academic archives) at the page level.
- **Real-time exhibit audio.** Those are synthesized in the browser via Web Audio API at play time — see `/src/exhibits/`.
- **User uploads.** The site does not accept user audio.

## Rights

Everything in `generated/` is MIT-licensed alongside the code that produces it. See [../LICENSE](../LICENSE) and [../docs/audio-catalog.md](../docs/audio-catalog.md) for per-file detail.
