## Adversarial review — uncommitted working-tree diff for v1.1.0 (2026-11-24)

M: authoring agent (operator-directed) · R: `general` subagent · C: `general` subagent, distinct instance
Reviewing commit: `e6d5218` (HEAD; the artifact was the working tree, reviewed before commit)
Model families: one family available in this environment. R and C ran as distinct
instances but **not** on different model families; the independence guarantee is
weaker than the preferred cross-family setup. Recorded per `ADVERSARIAL_REVIEW.md` Rule 5.

This diff changes the standard itself, so per `CONTRIBUTING.md` the review was run
against the standard's own v1.1.0 rules: R produced a seven-lens report, C audited
it with typed verdicts and missed-issue hunting, R responded to C, the author
reconciled every verdict, fixes were applied, and a separate post-fix re-review
was run. The review text below is the reconciled result, not a raw transcript.

### Findings and dispositions

1. **[MEDIUM] governance — f1 — index.html:703 (also README.md:24, CHANGELOG.md:45 before fix) — "signed `v1.0.1` tag" was false and the documented verification command failed.**
   Trigger: a reader runs `git tag -v v1.0.1` and gets an error instead of a signature check.
   Evidence: `git cat-file -t v1.0.1` → `commit` (lightweight tag); `git tag -v v1.0.1` → "cannot verify a non-tag object of type commit"; GitHub refs API `object.type: "commit"`; `git verify-commit v1.0.1` → Good signature from Andrew White. C: ACCEPT, keep MEDIUM. R: ACCEPT.
   **Disposition: ACCEPT — fixed.** Text now says the tag is lightweight but points to a GPG-verified commit, and directs readers to `git verify-commit v1.0.1`. Repo-wide grep for `signed tag`, `git tag -v`, `SHA-256` → zero hits.

2. **[LOW] spec-compliance — f2 — index.html:704 — "the SHA-256 published in the CHANGELOG" cited data that does not exist.**
   Evidence: grep for `sha-256`/64-hex across CHANGELOG.md, README.md, index.html found only the claim itself. C: ACCEPT. R: ACCEPT.
   **Disposition: ACCEPT — fixed** by removing the clause.

3. **[LOW] governance — f3 — CHANGELOG.md:12, index.html:955 — v1.1.0 published as a dated "Stable" release while `CONTRIBUTING.md` prerequisites were unmet.**
   C downgraded MEDIUM→LOW: the operator explicitly authorized the bump and publication and is the named NCO with a signed prior commit, but that is not a `CONTRIBUTING.md:35-36` signoff comment. R accepted the downgrade. Evidence: no issues or PRs; no signoff in git log; main has no branch protection or rulesets; rationale said signoff pending.
   **Disposition: MODIFY — partially fixed.** No signoff was fabricated and the release was not rolled back, because the operator explicitly authorized it. Instead, CHANGELOG.md, index.html and the rationale now state that the linked issue, PR review and NCO signoff comment remain pending operator action and are not implied by the release.

4. **[LOW] spec-compliance — f4 — README.md:21,51 vs README.md:60-70 — quickstart installs v1.0.1 (which has no challenger) while the install instructions call the loop non-optional.**
   C narrowed R's claim with `DISAGREE_EVIDENCE: SKILL.md:33-37` — the skill says "Spawn R and C" unconditionally, so "a repo with installed v1.0.1 file never spawns C" was unsupported. R accepted the narrowing. The narrower defect stands: `SKILL.md:26-28` makes the repo file authoritative, and the pinned template has no challenger rule.
   **Disposition: MODIFY — fixed.** README now states explicitly that the pinned template predates the loop and that the installed skill follows that file until a deliberate upgrade to v1.1.0.

5. **[LOW] spec-compliance — f5 — docs/v1.1-rationale.md:16,23,63 — rule-numbering drift from v1.0.1 to v1.1.0.**
   C extended to `:23` ("Fix-forward in our Rule 6"); R accepted. **Disposition: MODIFY — fixed.** Now "v1.1.0 Rule 3", "v1.0.1 Rule 6, now v1.1.0 Rule 7", and "Rule 7".

6. **[LOW] spec-compliance — f6 — docs/v1.1-rationale.md:24 claimed risk-tiered loop depth was partially adopted; the spec fixes one five-round cap.**
   C: ACCEPT; R: ACCEPT. **Disposition: ACCEPT — fixed** by changing the verdict to "Not adopted" and noting repos may add rounds in their own rules.

7. **[LOW] spec-compliance — f7 — docs/v1.1-rationale.md:21 said an arbitration-policy template would be added to the worked example; it was not.**
   C: ACCEPT; R: ACCEPT. **Disposition: ACCEPT — fixed** by marking it "Deferred — not implemented in v1.1.0".

8. **[LOW] spec-compliance — f8 (C's m1) — docs/v1.1-rationale.md:15 said cross-family review was "SHOULD, not MUST" while the same file and the spec say MUST where available.**
   R accepted. **Disposition: ACCEPT — fixed** to "MUST where available", with the same-family limitation recorded otherwise.

9. **[MEDIUM] process — f9 (C's m2) — the missing in-environment C pass was filed as UNVERIFIED, but a confirmed omission of a mandatory step is not an unresolvable suspicion.**
   R accepted the core but disputed C's description: the original R report did contain a model-family line, a reconciliation line, per-finding "would change mind", and an explicit statement that an R-only report does not satisfy Rule 5; the UNVERIFIED item was scoped to an unseen pass in another session. Resolved narrowly: the gap was the absent C pass itself.
   **Disposition: ACCEPT (narrowed) — cured.** C ran, R responded to all C verdicts, this record reconciles them, and a post-fix re-review was run. The one-family limitation remains recorded above.

10. **[LOW] spec-compliance — f10 (C's m3) — index.html:854 overclaimed that every design choice is traceable to the bibliography.**
    R accepted with corrected line number. **Disposition: ACCEPT — fixed.** The lede now limits traceability to empirical claims and points to the rationale for design decisions.

11. **[LOW] governance — f11 (C's m4) — CONTRIBUTING.md:20-21 claimed a protected `main` branch and merge queue that do not exist.**
    GitHub API: branch protection 404, rulesets `[]`. Pre-existing text, surfaced by the site's governance link. R accepted. **Disposition: ACCEPT — fixed** to state signatures are required by policy and that platform enforcement is configured separately by the operator.

### UNVERIFIED

- Cross-family independence: only one model family was available; R and C were distinct instances on the same family. Not promoted to a finding because it is recorded as a limitation of this environment, not a hidden defect.
- Narrow-viewport rendering of the new `.labels`/`.id`/`.hint` layout: no headless browser exercised; HTML validation showed no new errors.
- Full-body accuracy of the qualitative relevance tags in `docs/research-basis.md`: all 44 IDs/titles/authors were resolved against arXiv metadata; a few full texts were spot-checked, not all.
- Operation of the installed skill inside an actual agent runtime: not exercised; only its text was reviewed.

### Verdict per lens

- **security COVERED** — static Markdown/HTML/CSS diff; no new scripts, forms, handlers, URLs or secrets.
- **correctness COVERED** — f1, f2, f4–f8, f10, f11 fixed and re-verified; 44 arXiv IDs identical on site and in the bibliography; internal links resolve; `git diff --check` clean.
- **spec-compliance COVERED** — seven rules and seven lenses consistent across spec, skill, site and README; rule-numbering drift removed; html-validate delta zero.
- **performance COVERED** — static page; ~4.4 KiB gzip increase; no runtime work added.
- **abuse COVERED** — no new externally reachable surface.
- **operational COVERED** — quickstart `curl` resolves; `#research` nav target exists; Pages builds from `main//`; post-push build to be confirmed by the operator.
- **governance COVERED** — f1, f3, f9, f11; no NCO signoff fabricated; issue/PR/signoff explicitly recorded as pending.
- **governance (author's own policy)** — this review was performed by tooling controlled by the author. Under `CONTRIBUTING.md` it satisfies the PR-level review expectation but **does not** constitute the independent NCO signoff; that remains a human action.

### Post-fix re-review

A separate re-review pass verified all eleven dispositions against the working tree and found no new defects: **CLEAN**. The site and docs no longer claim a signed tag or a nonexistent SHA; the rationale matches the normative rule numbers and severity claims; governance notes are explicit; arXiv ID sets match (44/44); `git diff --check` exit 0.

### Governance note

The operator authorized the v1.1.0 version bump and publication. The linked issue, independent PR review and NCO signoff comment required by `CONTRIBUTING.md` were **not** performed here and are not implied by this review or release.
