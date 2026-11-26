# Changelog

Semantic versioning: the protocol (seven rules, core lenses, severity
taxonomy, output format, and the default reviewer–challenger loop) is the
stable API of this standard; template text and the repo-specific worked
example may tighten in patch releases.

## [Unreleased]

- Removed retired-version callouts from public-facing guidance: the site no
  longer repeats the upgrade note, and the README and skill describe stale
  repo-local templates generically as pre-1.1.0 copies.
- Site: added an above-the-fold proof strip and skill-install CTA, an FAQ
  section answering common objections, and SEO/social metadata (JSON-LD and
  refreshed titles and descriptions). Status and footer wording now match the
  stable, versioned framing.
- Adversarial-review fixes: quickstart now uses `curl -fsSL -o` with a version
  check and an overwrite warning; provenance guidance says tag verification runs
  in a clone and also compares the fetched file to the tagged template; the
  proof strip restores list semantics for assistive technology.

## [1.1.0] — 2026-11-24

Minor release: the reviewer–challenger loop becomes the default protocol, not
an optional add-on. Rationale and evidence are in
`docs/v1.1-rationale.md`; the arXiv sources are in
`docs/research-basis.md` and cited on the site.

- Added rule 5: every review runs the three-role loop (M author, R reviewer,
  C challenger of the review). Self-review does not satisfy it. R and C use
  different model families where available; otherwise the limitation is
  recorded.
- Added rule 6: a reconciliation entry for every C verdict (accept and fix,
  or reject with reason and evidence); no silent drops; unresolved
  `DISAGREE_CONCERN` on CRITICAL/HIGH findings escalates to the human NCO.
- Added typed disagreement to the contract: `DISAGREE_EVIDENCE: <code
  citation>` vs `DISAGREE_CONCERN: <epistemic objection>`; a confident
  rebuttal without a code citation cannot settle a concern.
- Rule 3 now requires reading the actual diff/commits rather than the
  authoring agent's summary. Rule 7 adds scope discipline: fixes stay inside
  the reviewed change; out-of-scope improvements are reported separately.
- Findings now carry a stable id, confidence in [0, 1], and what evidence
  would change the reviewer's mind; the report includes a Reconciliation
  section and records model families.
- Skill and bundled default protocol updated to v1.1.x; the skill spawns R
  and C by default. `docs/v1.1-rationale.md` records the
  decision table, counter-evidence, and governance status.
- Site: added a "Research basis" section citing 44 verified arXiv sources on
  agentic adversarial review, structured disagreement, multi-agent debate,
  LLM judging bias, benchmarks, and adversarial inputs; corrected the
  misattributed Du et al. ID in the Heym write-up.
- Site: rewrote the landing-page intro to lead with the AGENTS.md analogy and
  tightened the CI-skepticism paragraph; added Open Graph image and Twitter
  Card meta so shared links unfurl with a preview card.
- Published a GPG-signed `v1.1.0` tag. The quickstart pins to it
  (`git tag -v v1.1.0`), and that template includes the
  reviewer–challenger loop.
- Adversarial review of this diff: `docs/adversarial-review-2026-11-24.md`
  (reviewer R, challenger C, one model family available — limitation recorded).
  The operator authorized this release; the linked issue, PR review and
  `CONTRIBUTING.md` NCO signoff comment remain pending operator action and are
  not implied by this entry.

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
- The core lenses are no longer removable; customization is limited to the
  repo-specific section and additions.
- Added the canonical repo's own filled-in repo-specific rules as the worked
  example.
- Added LICENSE (MIT), CONTRIBUTING.md (issues-before-PRs, verified commits,
  NCO signoff), and this CHANGELOG.
- Site: quickstart pinned to the `v1.0.1` tag (lightweight; the commit it
  points to is GPG-signed); fixed dead canonical URL references; added
  governance section, GitHub links, OG meta tags, favicon, monospace code
  styling, and keyboard focus indicators.
- Wording: "machine-readable contract" softened to "agent-readable";
  intro's epistemic claim hedged to what CI can actually demonstrate;
  "Ownability" renamed "Repo-owned rules"; version/draft contradiction
  resolved (stable v1.x line, semver policy stated).

## [1.0.0] — 2026-10-16

Initial public release: six-rule protocol, six lenses, four-level severity
taxonomy, standard output format, repo-specific customization section, and
the adversarial-review.com landing page.

[1.1.0]: https://github.com/awdemos/adversarial-review/compare/v1.0.1...v1.1.0
[1.0.1]: https://github.com/awdemos/adversarial-review/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/awdemos/adversarial-review/releases/tag/v1.0.0
