# Architecture

This document describes how the Listening Room is actually put together, built and deployed. The site is a small set of self-contained prototype pages designed in Claude Design, exported as single HTML files, and served as static files from BlueHost.

---

## Stack

### Pages: single-file Claude Design exports

Each prototype is designed and iterated in Claude Design, then exported as one standalone HTML file and dropped into `src/`. The repo holds the exports, not their sources.

An export is a bundle: a small loader plus a manifest of compressed, inlined resources (scripts, fonts, AudioWorklet code) and the page template. Nothing is fetched from a CDN at run time — fonts and libraries are inside the file.

| Page | What's inside the bundle |
|---|---|
| `my-name-is-voice.html` (Mr Voice VTM) | JSX app transpiled in the browser by Babel, the vocal-tract engine as an AudioWorklet, pronunciation data (CMUdict subset, letter-to-sound rules), JetBrains Mono and Inter. About 1.9 MB. |
| `waveguide-lab.html` (Waveguide Lab) | Declarative template with React 18, a Kelly–Lochbaum waveguide as an AudioWorklet, JetBrains Mono and Archivo. About 350 KB. |

Rules that follow from this:

- **Don't hand-edit a bundle.** The markup and code are compressed and keyed by generated IDs. To change a page, change it in Claude Design and replace the file with a fresh export.
- **A page update is one file replaced**, plus its release notes (see below).
- Visual rules for the pages live in `CLAUDE.md` under "Aesthetic direction".

### Index: hand-written HTML

`src/index.html` is a plain hand-written page with inline CSS and no JavaScript. It lists the prototypes and links each one's supporting notes. This is the only page edited directly in the repo.

### Notes: Markdown served as-is

Release notes and guides for a prototype are Markdown files in `src/`, linked from the index next to the page they describe (currently `VTM2.2.0-2.8.0_Release-Notes.md` and `MIDI Guide.md`). They are deployed unrendered. When a new release-notes file supersedes an old one, the old file is removed and the index link updated.

### Audio: synthesized in the browser

All sound is generated at play time with the Web Audio API; the DSP runs in `AudioWorkletNode`s inside the bundles. The `AudioContext` starts only on a user gesture. Mr Voice also uses the Web MIDI API (Chrome) and keeps user settings, phrase library and controller maps in `localStorage`.

There are no pre-rendered audio files. `audio/` and `scripts/` exist as empty placeholders from the original plan for Python-generated examples; nothing reads from them today.

### No backend

Pure static files. No database, no server-side rendering, no analytics.

---

## Repo structure

```
vodechord-reference/
├── README.md
├── CLAUDE.md                      ← project context and style rules
├── LICENSE
├── CONTRIBUTING.md
├── .github/
│   ├── mlc_config.json            ← markdown link-check config
│   └── workflows/
│       └── ci.yml                 ← lint, link check, build, deploy
├── docs/
│   ├── vodechord-concept.md
│   ├── listening-app-spec.md
│   ├── audio-catalog.md
│   ├── architecture.md            ← you are here
│   └── design-briefs/
├── src/                           ← everything here is deployed
│   ├── index.html                 ← hand-written landing page
│   ├── my-name-is-voice.html      ← Claude Design export
│   ├── waveguide-lab.html         ← Claude Design export
│   ├── VTM2.2.0-2.8.0_Release-Notes.md
│   └── MIDI Guide.md
├── root-site/
│   └── index.html                 ← bare black page for vodechord.com root (not deployed by CI)
├── audio/                         ← placeholder, unused
├── scripts/                       ← placeholder, unused
└── build/                         ← CI output (gitignored)
```

---

## Build

There is no build step in the usual sense. CI copies `src/*` (and `audio/`) into `build/` and uploads that directory. If a `scripts/build.sh` is ever added, CI runs it instead of the copy.

Locally, any static file server pointed at `src/` shows the site as deployed. Opening the files directly mostly works, but AudioWorklet and Web MIDI want `http://localhost` or HTTPS.

---

## Deployment to BlueHost

1. A push to `main` triggers `.github/workflows/ci.yml`.
2. The build job produces `build/` and uploads it as an artifact.
3. The deploy job takes a `tar.gz` snapshot of the current remote directory, then runs `rsync -avz --delete` over SSH from `build/` to the server.
4. The site is served at https://vodechord.com/listening-room/.

Because of `--delete`, a file removed from `src/` is removed from the live site on the next deploy.

### Secrets

Stored as GitHub Actions secrets, never in the repo:

- `BLUEHOST_HOST` — server hostname
- `BLUEHOST_USER` — SSH username
- `BLUEHOST_SSH_KEY` — private key
- `BLUEHOST_PATH` — target directory (`.../listening-room/`)

If the secrets are missing, the deploy step is skipped with a warning.

### Site root

The SSH account is jailed to the `listening-room` directory, so CI cannot write to the vodechord.com root. `root-site/index.html` is kept in the repo for reference and is placed on the server by hand.

### Rollback

The upload is not atomic. To roll back, revert the commit and push; the pre-deploy snapshot on the server is the fallback.

---

## CI pipeline

Triggers: every push runs the checks; only a push to `main` deploys.

| Job | What it does today |
|---|---|
| **Lint** | shellcheck, plus ruff / eslint / stylelint if `scripts/`, `src/js/` or `src/css/` have content. With the current layout these have nothing to check. |
| **Audio reproducibility** | Runs `scripts/verify_reproducibility.py` if present; otherwise skips. |
| **Link check** | Checks links in every Markdown file in the repo, including the notes in `src/`. |
| **Build** | Copies `src/*` and `audio/` to `build/`. |
| **Deploy** | `main` only. Needs build, audio reproducibility and link check to pass. |

A broken link in any Markdown file blocks the deploy.

---

## Browser support

- **Target:** current desktop Chrome. Mr Voice's MIDI features need the Web MIDI API.
- Safari, Firefox and Edge run the audio (AudioWorklet is supported in all of them); MIDI support varies.
- **Mobile:** not a target for the prototypes. The index page is responsive.
- Wired audio output is recommended; Bluetooth audio adds enough delay to make the instruments unplayable.

---

## Updating a prototype

1. Export the new version from Claude Design and replace the file in `src/`.
2. Add or replace its release notes (and any guide) in `src/`.
3. Update the version tag and note links on that page's row in `src/index.html`.
4. Commit with the release notes summarized in the message, and push to `main`.
5. Check the Actions run, then the live page.

---

*Architecture doc version: October 10, 2026 — Rewritten for the Claude Design single-file export stack.*
