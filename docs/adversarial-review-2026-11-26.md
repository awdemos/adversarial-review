## Adversarial review — working-tree content refresh vs HEAD (2026-11-26)

**Scope:** the uncommitted working-tree diff in `/var/home/a/code/adversarial-review-site` affecting `index.html`, `README.md`, `CHANGELOG.md`, `docs/review-2026-10-17.md`, `docs/v1.1-rationale.md`, and `skill/adversarial-review/SKILL.md`, reviewed against the repo's binding `ADVERSARIAL_REVIEW.md` Version 1.1.0 and `skill/adversarial-review/default-protocol.md`. The untracked `docs/adversarial-review-2026-11-25.md` was read as prior context only, not as artifact. This report and the fixes below are the post-review additions; the artifact was frozen during R↔C.

**M:** `opencode/big-pickle` (orchestrator/editor, edits only after R↔C stabilized) · **R:** `opencode/seaglass-alpha-free` (reviewer subagent, distinct instance, read-only) · **C:** `opencode/big-pickle` (challenger subagent, distinct instance, read-only, operator-directed)

**Reviewing commit:** `6cc89322c6b25738a4be3054f65bafacfb37f44b` (`v1.1.0-2-g6cc8932`; HEAD == `origin/main`; working tree dirty).

**Model-family limitation (recorded, per standard rule 5):** R and C are distinct instances but both are served under the `opencode` provider, so cross-family independence is weaker than the standard prefers. Two cross-family C attempts were made and failed for environmental reasons: `zai-coding-plan/glm-5.4` → "Insufficient balance or no resource package"; `cliproxy/kimi-k2.5` → `ECONNREFUSED`. The operator then directed "just use big pickle." No claim of cross-family independence is made.

---

### Evidence produced before findings

- `curl -fsSL https://raw.githubusercontent.com/awdemos/adversarial-review/v1.1.0/ADVERSARIAL_REVIEW.md` → HTTP 200, `Version 1.1.0`, byte-identical to the repo copy and to `git show v1.1.0:ADVERSARIAL_REVIEW.md` (SHA-256 `7144549e…f4c51fa1e2fe4f4202b93775daddbb82cd0f6`).
- `git tag -v v1.1.0` → good EDDSA signature from Andrew White; annotated, signed tag. Recent commits `%G?=G`.
- `git describe --tags --always --dirty` → `v1.1.0-2-g6cc8932-dirty`; `git rev-list --count v1.1.0..HEAD` → `2`.
- `python3 html.parser` on `index.html` → parses, no duplicate ids, 12 internal anchors all resolve (including new `#faq`), JSON-LD parses (`SoftwareApplication`, `version=1.1.0`).
- 44/44 unique arXiv links in `#research` match `docs/research-basis.md` and live arXiv API metadata.
- Live `https://adversarial-review.com/` byte-matches committed HEAD, not the working tree; the diff was not deployed during review.
- `git diff --check` → clean.

---

### Findings and reconciliation

1. **[LOW] web/spec-compliance — f1 — `index.html:745`** — Hero eyebrow says "An open standard proposal" while Status/footer/JSON-LD use "stable, versioned". **C verdict: REJECT** with `DISAGREE_EVIDENCE: CHANGELOG.md:15-16` ("**Status and footer wording** now match the stable, versioned framing" — explicitly scoped, satisfying R's own would-change-mind condition), plus `git show HEAD:index.html:684` showing the eyebrow is unchanged and absent from the diff. **Disposition: REJECT — R's finding revised/dropped in light of cited code.** Recorded as out-of-scope copy hygiene, not a defect of this diff.

2. **[MEDIUM] operational/security — f2 — `index.html:784`, `README.md:21`** — Quickstart used bare `curl -O <pinned URL>`: silent overwrite of a customized `ADVERSARIAL_REVIEW.md` with no `-f`/`-o`/backup/version check, and an HTTP body written as the contract with exit 0 on error. **C verdict: ACCEPT, promoted LOW → MEDIUM.** C reproduced both halves locally (sentinel rules destroyed; local 503 body written as the file with `curl` exit 0). **Disposition: ACCEPT + FIXED** in `index.html` and `README.md`: `curl -fsSL -o ADVERSARIAL_REVIEW.md …` + `grep -m1 '^Version ' …`, plus an explicit overwrite warning. Re-verified: fetch is byte-identical; on a local 503 the new command exits 22 and leaves an existing valid file unchanged.

3. **[MEDIUM] spec-compliance/governance — f3 — `index.html:787-789`, `README.md:24-25`** — "verify it with `git tag -v v1.1.0`" fails in the exact audience the quickstart creates: a fresh non-clone repo returns `error: tag 'v1.1.0' not found`, exit 1; even in a clone it verifies the tag object, not the fetched file. The signed-release claim itself is true. **C verdict: ACCEPT, promoted LOW → MEDIUM.** **Disposition: ACCEPT + FIXED**: guidance now says to run verification in a clone of `awdemos/adversarial-review`, and adds `git show v1.1.0:ADVERSARIAL_REVIEW.md | diff - ADVERSARIAL_REVIEW.md` to verify fetched content.

4. **[LOW] spec-compliance — f4 — `index.html:786-789`, `CHANGELOG.md:8-16`, `README.md:29-42`, `SKILL.md:26-32`** — Removal of the site's retired-version upgrade note leaves site-only pre-1.1.0 readers without an on-page nudge; combined with f2 the site command could overwrite a stale repo-local copy. No protocol regression: `ADVERSARIAL_REVIEW.md` untouched, README keeps a generic "predates 1.1.0" upgrade path, SKILL rule 1 flags any older version. **C verdict: ACCEPT (LOW).** **Disposition: ACCEPT + FIXED** with a single short site pointer to the README upgrade section (no retired version number repeated on-page).

5. **[LOW] web/spec-compliance — f5 — `index.html:801`** — Hero says "machine-readable" while the canonical template says "agent-readable contract"; pre-existing and not touched by the diff. **C verdict: MODIFY** with `DISAGREE_EVIDENCE: git diff -- index.html` has no hunk at that line and `git show HEAD:index.html:727` is identical. **Disposition: MODIFY — downgraded from a finding against this diff to an out-of-scope hygiene note.** Not implemented, per fix-forward scope discipline.

6. **[LOW] web/a11y — c1 — `index.html:344`, `index.html:763`** (new finding from C) — The diff adds `.proof { list-style: none; }` and `<ul class="proof" aria-label="Project at a glance">` without `role="list"`, which strips list semantics in Safari/VoiceOver. Evidence: `git diff -U0 -- index.html` shows both additions; `grep -n 'role="list"' index.html` → no output. **Disposition: ACCEPT + FIXED** by adding `role="list"` to the `<ul>`. Re-verified present.

No CRITICAL/HIGH findings were open at any point; no unresolved `DISAGREE_CONCERN` attached to CRITICAL/HIGH; no NCO escalation (NCO of record: Andrew White).

---

### UNVERIFIED (not dropped, not promoted)

- **u1** `og-image.png` rendered text vs the new titles/alt cannot be confirmed (no image input/OCR/headless browser); format, size, URL, and alt consistency were checked.
- **u2** `<title>` vs `og:title`/`twitter:title` vs body pitch differ; shortening is defensible, no canonical blurb was supplied to settle it.
- **u3** GitHub Pages deploy source and post-merge deployment cannot be inspected; live byte-matches committed HEAD, not the working tree.
- **u4** GitHub-side governance enforcement (branch protection, issue/PR, NCO signoff comments) cannot be inspected; site honestly records signoff as pending.
- **u5** 44/44 arXiv IDs/titles/authors match live API metadata; full texts were not exhaustively re-read.
- **u6** "no size exemption" rule shorthand is not a literal rule name in the template.
- **u7** GitHub `/blob/` returned transient 503s during review (raw pinned URL stayed 200).
- **u8** No real browser: contrast, reflow, focus, screen-reader, and social-card unfurl rendering were not exercised.
- **u9** Model-family independence is weaker than the standard's preference (see header).
- **u10** Historical `git verify-commit v1.0.1` note from the prior review was not re-run for this change.

---

### Verdict per lens

- **security — COVERED** (static HTML/CSS/JS + static JSON-LD; no forms/auth/secrets/server/dynamic sinks; literal `https:` links only; f2 provenance hardening applied and re-tested).
- **correctness — COVERED** (HTML parses; no duplicate ids; all 12 anchors resolve; JSON-LD valid; quickstart executes byte-identically; f3 reproduction verified and corrected).
- **spec-compliance — COVERED** (ADVERSARIAL_REVIEW.md byte-identical to signed v1.1.0 tag; protocol, core lenses, loop, severity taxonomy, output format and reconciliation untouched; README/SKILL/CHANGELOG claims cross-checked; f1/f3/f4/f5 reconciled).
- **performance — COVERED** (single static page; no build or hot path; no added runtime fetches or binary bloat).
- **abuse — COVERED** (no new user-facing surface; quickstart overwrite/poisoning path analyzed and hardened; provenance claim tested; no protocol downgrade).
- **operational — COVERED** (deployment/freshness facts recorded; quickstart verified in a clean sandbox and against a local 503; rollback = revert; u3/u7 recorded).
- **governance — COVERED** (signed tag and commits verified; MIT license, NCO name, and pending signoff truthfully represented; C verdicts fully reconciled; u4 attached here).
- **web (repo extra) — COVERED with a11y limitation** (meta/OG/JSON-LD and anchors checked; c1 fixed and re-verified; u1/u2/u8 recorded; no real browser used).

No lens was skipped. "COVERED" with limitations means the listed checks were exercised and the named limitation is explicit.

---

### Post-fix re-review

After the R↔C inner loop stabilized, M applied only the accepted in-scope fixes and re-ran: `git diff --check` (clean); HTML/anchor/JSON-LD parse (ok, no broken anchors); `role="list"` present; documented quickstart in a clean `git init` sandbox (byte-identical template, `Version 1.1.0`, sentinel customization overwritten with warning documented); local-503 test (`curl -fsSL` exit 22, existing valid file preserved); and tag/template SHA equality. These fixes are **uncommitted**, consistent with the repo's governance rule that signed commits and NCO signoff are operator actions; no signoff or merge claim is made here.

**Not checked:** live browser rendering, real social-card unfurl, GitHub-side settings/enforcement, full text of all 44 arXiv sources, and deployment of this diff. These are listed under UNVERIFIED rather than treated as clean.
