# Architecture

This document describes how the Listening Room is actually put together, built and deployed. The site is a small set of self-contained prototype pages designed in Claude Design, exported as single HTML files, and served as static files from BlueHost.

---

## Stack

### Pages: single-file Claude Design exports

Each prototype is designed and iterated in Claude Design, then exported as one standalone HTML file and dropped into `src/`. The repo holds the exports, not their sources.

An export is a bundle: a small loader plus a manifest of compressed, inlined resources (scripts, fonts, AudioWorklet code) and the page template. Nothing is fetched from a CDN at run time — fonts and libraries are inside the file.

| Page | What's inside the bundle |
|---|---|
| `mr-voice/<version>/Mr Voice VTM<version> (standalone).html` (Mr Voice VTM) | JSX app transpiled in the browser by Babel, the vocal-tract engine as an AudioWorklet, pronunciation data (CMUdict subset, letter-to-sound rules), JetBrains Mono and Inter. About 1.9 MB. |
| `waveguide-lab.html` (Waveguide Lab) | Declarative template with React 18, a Kelly–Lochbaum waveguide as an AudioWorklet, JetBrains Mono and Archivo. About 350 KB. |

Rules that follow from this:

- **Don't hand-edit a bundle.** The markup and code are compressed and keyed by generated IDs. To change a page, change it in Claude Design and replace the file with a fresh export.
- **Mr Voice ships as versioned releases.** Each release is a folder, `src/mr-voice/<version>/`, holding the exported page and its own copy of the docs that came with it. Releases stay live side by side (see "Adding a release" below).
- Visual rules for the pages live in `CLAUDE.md` under "Aesthetic direction".

### Index: hand-written HTML

`src/index.html` is a plain hand-written page with inline CSS and no JavaScript. It links the latest release of each prototype, that release's docs, and earlier releases. `src/my-name-is-voice.html` is a hand-written redirect to the latest Mr Voice release, which keeps the original URL working. These two are the only pages edited directly in the repo.

### Release docs: served as exported

Each release folder holds the docs that came in its zip: release notes and the MIDI guide as Markdown (deployed unrendered), plus HTML guides such as `Phrase Guide.html` and `Workflows.html`. Filenames are kept exactly as exported, because the guides link to each other by filename. A release folder is frozen once committed, and a fix becomes a new version.

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
│   ├── my-name-is-voice.html      ← hand-written redirect to the latest Mr Voice release
│   ├── waveguide-lab.html         ← Claude Design export
│   ├── *.zip                      ← incoming release zips (gitignored, never deployed)
│   └── mr-voice/
│       ├── 2.8.0/                 ← my-name-is-voice.html, MIDI Guide.md, Release Notes 2.2.0-2.8.0.md
│       ├── 2.14.0/                ← Mr Voice VTM2.14.0 (standalone).html, Phrase Guide.html,
│       │                            Workflows.html, MIDI Guide.md, Release Notes *.md
│       └── 3.0.0/                 ← latest; same set of files, notes in Release Notes 3.0.0.md
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

Because of `--delete`, a file removed from `src/` is removed from the live site on the next deploy. Old releases stay live because they stay in `src/`. The server mirrors the folders, so a release is served at e.g. `/listening-room/mr-voice/2.14.0/`, with its docs alongside.

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

## Adding a release

1. Export the release from Claude Design as a zip (the standalone page plus its docs) and drop it into `src/`. `src/*.zip` is gitignored, so the zip itself is never committed or deployed.
2. Unpack it into `src/mr-voice/<version>/` without the zip's top-level folder. Leave the filenames unchanged.
3. Point `src/my-name-is-voice.html` (both the meta refresh and the fallback link) at the new release's page.
4. In `src/index.html`, point the Mr Voice row, its version tag and its doc links at the new folder, and add the previous release to the "Previous" links.
5. Commit with the release highlights in the message, and push to `main`.
6. Check the Actions run, then the live page. Delete the zip locally, or keep it.

Never edit or remove a committed release folder. Ship a new version instead.

---

*Architecture doc version: October 18, 2026 — Versioned release folders for Mr Voice.*
