# Contributing to Vodechord Reference

This is a public project, but it's primarily personal. It exists to inform design decisions on the Vodechord instrument. Contributions are welcome in spirit, but the bar for inclusion is "does this help the project's author(s) hear something they need to hear" — not general usefulness, comprehensiveness, or pedagogical completeness.

## What contributions are welcome

- **Corrections.** If an audio example is mislabeled, a description is technically wrong, or a source attribution is missing, open an issue or a PR.
- **New audio references.** If you know of a recording that demonstrates a specific acoustic feature (jitter, spectral tilt, nonlinear phonation, etc.) better than what's in the catalog, propose it in an issue. Include the feature, the recording, the specific timestamp or passage, and why you think it's a better reference than what's there.
- **Generation recipe improvements.** If a script in `scripts/` produces an example that could be clearer, more isolated, or more faithful to the real acoustic phenomenon, PR it.
- **Accessibility improvements.** Anything that makes the exhibits easier to perceive, navigate, or understand for listeners with different abilities.

## What contributions are not welcome (right now)

- **New features or exhibits unrelated to Vodechord's design questions.** The scope is narrow on purpose.
- **Stylistic rewrites of the concept doc or the listening-app spec.** These reflect the author's evolving design thinking and shouldn't be churned by outside editing.
- **Framework migrations or build tool changes.** The stack is deliberately minimal (see `docs/architecture.md`).

## Process

1. Open an issue first for anything non-trivial. Describe what you want to change and why.
2. For audio contributions, include source attribution and confirm the license or fair-use basis.
3. Keep PRs focused. One feature or fix per PR.
4. The maintainer's taste is the final arbiter. This isn't a democracy. If you want your own reference site with different choices, fork it.

## Code of conduct

Be kind. Assume good faith. Don't be a jerk about audio opinions — taste is personal, the project's taste is the project's taste, and reasonable listeners can disagree on what sounds good.
