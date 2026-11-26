## Adversarial review — working tree / current HEAD (2026-11-25)

M: opencode/big-pickle (authoring agent for this report) · R: opencode/big-pickle (reviewer pass) · C: opencode/big-pickle (challenger pass)
Reviewing commit: 6cc8932 (HEAD -> main, origin/main, origin/HEAD) "Warn against cached raw main URL in quickstart"

Scope: `index.html`, `README.md`, `CHANGELOG.md`, `ADVERSARIAL_REVIEW.md`, `docs/v1.1-rationale.md`, `skill/adversarial-review/SKILL.md`, `og-image.png` presence/metadata, and local git state. This is a pre-refresh content audit requested by the operator ("what next? run AR on that") to guide an upgrade of adversarial-review.com.

Model families: same family/instance limitation recorded. R and C were executed as separate adversarial passes in the same session because no cross-family runtime was available. Independence is weaker than the protocol's preferred setup; findings should be treated accordingly.

### Findings

1. [MEDIUM] spec-compliance — f1 — `index.html:962-984` — Status section presents v1.1.0 release-governance debt as current landing-page status without distinguishing post-tag HEAD changes. Evidence: `git describe --tags --always --dirty` => `v1.1.0-2-g6cc8932`; `git rev-list --count v1.1.0..HEAD` => `2`; `CHANGELOG.md:8-13` contains an `[Unreleased]` section documenting upgrade-clarification changes already present on HEAD. The site Status section (`index.html:966-984`) states "Stable under semantic versioning v1.1.0" and includes a governance note that "issue, PR review and NCO signoff... are still pending operator action" for that release, but gives no signal that the deployed/content state may be ahead of the signed tag. Confidence: 0.74. Would change mind if: the site is intentionally deployed only from the signed v1.1.0 tag and never from main/HEAD, and that deployment policy is stated on-page or in repo docs. Fix: add a small, factual "current as of tag/main" note or separate "Release governance" from "Current site/content state"; if unreleased changes are not deployed, state deployment source explicitly.

2. [LOW] correctness / spec-compliance — f2 — `index.html:1025`, `README.md`/`CHANGELOG` messaging — Footer labels the project "an open standard proposal" while hero/status/body copy presents it as a stable, versioned standard/protocol. Evidence: footer text at `index.html:1025`: `ADVERSARIAL_REVIEW.md — an open standard proposal`; contrast with `index.html:963` "Stable under semantic versioning v1.1.0" and protocol language throughout. Confidence: 0.68. Would change mind if: "proposal" is the intentional legal/positioning term despite semver-stable protocol, and that distinction is explained. Fix: align footer wording with current status (e.g., "open standard" or "open, versioned standard") or explicitly define "proposal" vs "stable protocol".

3. [LOW] governance — f3 — `README.md:14`, `docs/v1.1-rationale.md:7,92-101` — Governance-pending language is duplicated across README, rationale, and site; drift risk during content refresh. Evidence: README line 14 says "governance signoff still pending"; rationale lines 7 and 92-101 state pending issue/PR review/NCO signoff; site status note repeats same pending state (`index.html:976-984`). All currently consistent, but three manually maintained copies create reconciliation risk. Confidence: 0.63. Would change mind if: a single canonical governance-status source is generated/included, or maintainers confirm a deliberate duplication policy. Fix: designate one canonical governance-status location and link others to it, or add a maintenance note/checklist in CONTRIBUTING/docs.

4. [LOW] operational / web — f4 — `index.html:16-27`, `og-image.png` — OG/Twitter image exists locally, but no automated check verifies social-preview freshness/validity beyond file presence. Evidence: `ls -la og-image.png` shows 74468-byte PNG (1200x630, 8-bit RGB) at HEAD; `index.html:17-26` references absolute `https://adversarial-review.com/og-image.png`. No CI/script in repo validates image dimensions/content match current branding/version. Confidence: 0.60. Would change mind if: deployment pipeline or external check validates OG image/card rendering. Fix: add a lightweight validation script/check (dimensions, file size/type, expected URL) to release/content-refresh checklist.

### UNVERIFIED

- External link liveness and redirect correctness for all arXiv/GitHub/loopsandgraphs links were not exhaustively HTTP-checked. Attempted: extracted 60+ URLs from `index.html`; did not run network validation against each endpoint to avoid noisy/slow external requests in this pass. Why unsettled: link rot or redirect changes require live HTTP checks. What would settle it: run a link checker (e.g., `lychee`/`curl -I`) against extracted URLs and record status codes.
- GitHub Pages deployment configuration/source-of-truth was not inspected. Attempted: reviewed repo files present locally; no `.github/workflows` or Pages config file found in repo root. Why unsettled: cannot confirm whether site deploys from `main`, tag, or another mechanism. What would settle it: inspect GitHub repo Pages settings / deployment workflow in remote config.
- Commit signature verification for recent commits was not performed. Attempted: observed commits exist post-tag; did not run `git log --show-signature` or `git tag -v` verification in depth. Why unsettled: governance claim on site describes process; actual signature status of HEAD commits unknown. What would settle it: run signature verification for tag and recent commits.

### Reconciliation

- f1: ACCEPT (content clarity gap) — The claim is evidence-backed (HEAD is 2 commits past v1.1.0 with Unreleased notes). C objection: this may be intentional because site states "stable under semantic versioning v1.1.0" as protocol version, not deployment freshness; governance note is historically accurate. Disposition: keep as MEDIUM/LOW boundary; downgraded from potential HIGH confusion to MEDIUM because the page does state v1.1.0 clearly but lacks deployment/state context.
- f2: ACCEPT (wording consistency) — C objection: "open standard proposal" may be deliberate positioning. Disposition: accepted as LOW; fix should be an intentional copy decision, not silent rewrite.
- f3: ACCEPT — C labeled `DISAGREE_CONCERN`: duplication is manageable in a small static repo. Disposition: accepted as LOW maintenance risk; no code/data bug.
- f4: ACCEPT — C labeled `DISAGREE_CONCERN`: missing automated OG check is common for static sites. Disposition: accepted as LOW operational hardening opportunity.

No CRITICAL/HIGH findings. No unresolved `DISAGREE_CONCERN` on CRITICAL/HIGH findings; no NCO escalation required.

### Verdict per lens

- security: COVERED (clean: static HTML/CSS/JS only; no form/input handling, no secrets, no dynamic server code; external links are literal https URLs; inspected `index.html` script for DOM manipulation — only equalizes card heights, no external data). Commands/evidence: `sed`/`rg` inspection of `index.html`, `README.md`; reviewed JS at `index.html:989-1030`.
- correctness: COVERED (2 LOW findings f2,f4; anchor refs checked with internal anchor extraction — no missing anchors; HTML parsed via `python3 html.parser.HTMLParser().feed(...)` => `html-parse-ok`). Commands: anchor extraction/compare, python HTML parse.
- spec-compliance: COVERED (1 MEDIUM f1, 1 LOW f2, 1 LOW f3; verified quickstart tag URL present and template fetch returns `Version 1.1.0`; checked version strings across README/CHANGELOG/index/template). Commands: `curl -fsSL .../v1.1.0/ADVERSARIAL_REVIEW.md` to `/tmp/ar-template-check.md`, `grep` version/quickstart lines, `git describe/rev-list`.
- performance: COVERED (clean: single static page, inline CSS/JS, one 74KB PNG OG image; no build/runtime hot path in repo). Evidence: `ls -la og-image.png`, file size/type check.
- abuse: COVERED (clean: no user-generated content, no forms, no auth, no executable backend; lowest abuse surface is misleading copy/social preview drift, captured under spec-compliance/operational). Evidence: repo file inventory and static-site inspection.
- operational: COVERED (1 LOW f4; checked git state, tags, unreleased changelog, and absence of deployment workflows in repo). Commands: `git status`, `git log`, `git describe`, `git tag --points-at HEAD`, `git rev-list`, `ls docs`.
- governance: COVERED (1 LOW f3; CONTRIBUTING/CHANGELOG/LICENSE present; NCO named in README/template; pending governance signoff transparently documented but duplicated). Evidence: `README.md:94`, `ADVERSARIAL_REVIEW.md:183-186`, `docs/v1.1-rationale.md` governance status section.

### Not checked

- Live HTTP status for all external links (arXiv, GitHub, loopsandgraphs.dev, agents.md).
- GitHub Pages deployment settings / actual published site content vs local HEAD.
- GPG/SSH signature verification for commits and tags.
- Browser rendering/visual regression, screen-reader behavior, and social-card unfurl rendering on real platforms.
- Legal/licensing review beyond presence of MIT LICENSE file.
