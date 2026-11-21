# Changelog

Semantic versioning: the protocol (six rules, six core lenses, severity
taxonomy, output format) is the stable API of this standard; template text
and the repo-specific worked example may tighten in patch releases.

## [Unreleased]

- Site: rewrite landing-page intro — lead with the AGENTS.md analogy,
  tighten the CI-skepticism paragraph, keep the one-file punchline.

- Site: add Open Graph image and Twitter Card meta (`twitter:card`,
  `og:image`) so shared links unfurl with a preview card.

## [1.0.1] — 2026-10-17

First revision after publication. v1.0.0 was subjected to a 347-agent
adversarial review (46 content units × 7 lenses: accuracy, readability,
comprehensiveness, consistency, self-application, governance, evasion); the
raw findings are preserved in `docs/review-2026-10-17.md`. Changes:

- Added the `governance` lens to the core set (contributor-process
  integrity: enforced signoffs, verified commits, issues-before-PRs).
- Rule 2 now requires each core lens be marked COVERED or SKIPPED with a
  stated reason; silent skipping is a failed review.
- Rule 3: UNVERIFIED items must record what was attempted and why the
  suspicion could not be settled; they may not be promoted to findings or
  dropped.
- Rule 5: every finding needs reproducible evidence (no "(log attached)");
  clean verdicts must state what was exercised; the report must declare its
  scope and anything deliberately not checked.
- Output format: added reviewer identity, review scope, an UNVERIFIED
  section, and fixed the sample findings to model the evidence rule
  (including a severity that matches its own definition).
- The six core lenses are no longer removable; customization is limited to
  the repo-specific section and additions.
- Added the canonical repo's own filled-in repo-specific rules as the worked
  example.
- Added LICENSE (MIT), CONTRIBUTING.md (issues-before-PRs, verified commits,
  NCO signoff), and this CHANGELOG.
- Site: quickstart pinned to the signed `v1.0.1` tag; fixed dead canonical
  URL references; added governance section, GitHub links, OG meta tags,
  favicon, monospace code styling, and keyboard focus indicators.
- Wording: "machine-readable contract" softened to "agent-readable";
  intro's epistemic claim hedged to what CI can actually demonstrate;
  "Ownability" renamed "Repo-owned rules"; version/draft contradiction
  resolved (stable v1.x line, semver policy stated).

## [1.0.0] — 2026-10-16

Initial public release: six-rule protocol, six lenses, four-level severity
taxonomy, standard output format, repo-specific customization section, and
the adversarial-review.com landing page.

[1.0.1]: https://github.com/awdemos/adversarial-review/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/awdemos/adversarial-review/releases/tag/v1.0.0
