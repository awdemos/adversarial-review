# Default Adversarial Review Protocol (ADVERSARIAL_REVIEW.md v1.1)

Bundled fallback for the `adversarial-review` skill, used when the reviewed
repository has **no** `ADVERSARIAL_REVIEW.md` of its own. It mirrors the open
standard (https://github.com/awdemos/adversarial-review, MIT). If the repo
declares its own `ADVERSARIAL_REVIEW.md`, that file wins and this one is not
used. The reviewer–challenger loop is the default, not an option.

## The protocol

When asked for an adversarial review (or when finishing a non-trivial change
in a repo bound by this protocol), you MUST:

1. **Review as an adversary, not a linter.** Assume the code is guilty until
   proven innocent. Find ways the change fails, breaks invariants, or can be
   abused — not reasons it works. Every finding must name a concrete trigger:
   a code path, a command, or a reproduction. Hypothetical scenarios with no
   trigger are UNVERIFIED, not findings.
2. **Run independent passes, one lens at a time.** Cover every core lens
   below in a separate pass. List each as COVERED or SKIPPED with a one-line
   justification; a silent skip fails the review.
3. **Verify against the artifact, not a description of it.** Read the actual
   diff, commits, and surrounding code; a summary written by the authoring
   agent is not evidence. Unconfirmed suspicions go to UNVERIFIED with what
   was attempted and why it could not be settled — never promoted, never
   dropped.
4. **Rate severity honestly.** CRITICAL (exploitable security hole, data
   loss, broken core guarantee; must fix before merge), HIGH (real defect or
   guarantee weakened under plausible conditions), MEDIUM (correctness or
   robustness gap with a concrete failure mode), LOW (hardening, clarity,
   hygiene). Inflate nothing.
5. **Run the reviewer–challenger loop.** You are the authoring agent M. Spawn
   an independent reviewer R to propose findings as structured data, then a
   challenger C that audits R's review rather than re-reviewing from scratch.
   R and C MUST use different model families where more than one is
   available; otherwise record the same-family limitation in the report.
   Freeze the artifact during the R↔C exchange, cap it at five rounds, and
   edit only after the review stabilizes. A finding-free review still needs
   per-lens evidence of what was exercised.
6. **Require typed disagreement.** C returns ACCEPT, REJECT, MODIFY, or
   NEEDS_EVIDENCE per finding id, plus missed issues with code citations.
   Objections are labelled `DISAGREE_EVIDENCE: <code citation>` or
   `DISAGREE_CONCERN: <epistemic objection>`. On evidence disagreements R
   revises from the cited code; on concerns R must cite code confirming the
   bug (keep it) or refuting it (drop it). A confident rebuttal without code
   does not settle an objection.
7. **Reconcile and fix forward in scope.** Record the disposition of every C
   verdict: accept and fix, or reject with reason and evidence. No silent
   drops. Escalate unresolved concerns on CRITICAL/HIGH findings to a human
   arbiter/NCO. Fix CRITICAL/HIGH first, re-review the diff after fixes, and
   keep fixes within the scope of the reviewed change; report out-of-scope
   improvements separately.

## Roles

| Role | Job | Constraint |
|------|-----|------------|
| M (you) | Produce and edit the artifact; update the change log | No edits during the inner loop |
| R (reviewer subagent) | Propose findings per lens with severity and confidence | Reads the artifact; cites code |
| C (challenger subagent) | Dispute findings, check severities, hunt misses | Different model family where available |

## Core lenses

| Lens | Hunts for |
|------|-----------|
| `security` | Injection, authn/authz gaps, secret handling, unsafe deserialization, dependency confusion, path traversal, tamper-evasion of integrity guarantees |
| `correctness` | Edge cases, error paths, race conditions, off-by-one, broken invariants, unhandled states |
| `spec-compliance` | Behavior that diverges from documented contract/README; tests that don't test what they claim |
| `performance` | Complexity regressions, unbounded growth, allocation hot paths, startup/latency cost, binary/dependency bloat |
| `abuse` | Ways the feature can be used against its intent: spam, forgery, downgrade, replay, truncation, griefing |
| `operational` | Deploy/persistence failure modes, migration hazards, observability gaps, rollback story |
| `governance` | Contributor-process integrity: signoff requirements actually enforced, verified/signed commits, issues-before-PRs, license and version hygiene, who may change the rules |

Repos may add lenses; they may not remove the core ones.

## Output format

```markdown
## Adversarial review — <scope: commit range / files> (<date>)

M: <agent id> · R: <reviewer id, model family> · C: <challenger id, model family>
Reviewing commit: <hash>

1. [CRITICAL] security — f1 — src/auth.rs:88 — session token accepted after
   logout. Evidence: `curl -X POST ...` reproduced in 2 requests; transcript
   below. Confidence: 0.93. Would change mind if: revocation checked in
   middleware. Fix: check revocation list in middleware, not only at issue time.

UNVERIFIED: suspected race in src/queue.rs:120 — attempted reproduction with
`stress -c 8` for 10 min, inconclusive. Needs a machine with >8 cores.

Reconciliation:
- f1: ACCEPT — fixed in <fix commit>; diff re-reviewed.
- f2: NEEDS_EVIDENCE — DISAGREE_CONCERN on a HIGH finding; escalated to a human.

Verdict per lens: security COVERED (1 finding), correctness COVERED (clean:
212 paths read, error branches exercised via test X), performance COVERED
(clean: benchmark command and output below), abuse SKIPPED (no externally
reachable surface in this diff) — reason recorded, spec-compliance COVERED
(1 doc drift), operational COVERED (clean: deploy path re-run in staging),
governance COVERED (clean).
Not checked: Windows builds (out of scope for this diff).
Model families: distinct / same-family limitation recorded.
```

Write the report to `docs/adversarial-review-<YYYY-MM-DD>.md` in the reviewed
repo unless the user directs otherwise.
