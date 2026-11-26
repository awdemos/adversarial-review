---
name: adversarial-review
description: Use when the user asks for an adversarial review, security review, deep review, or red-team pass over code, a diff, or a PR; when work is about to be called done or merged and might be rubber-stamped; or when the repo contains an ADVERSARIAL_REVIEW.md file declaring this standard. Runs the default reviewer–challenger loop.
---

# Adversarial Review

## Overview

Review as an adversary, not a linter: assume the code is guilty until proven
innocent; every finding must prove itself with reproducible evidence.
Implements the ADVERSARIAL_REVIEW.md open standard (v1.1.x). The default
protocol is a three-role loop: authoring agent M, reviewer R, challenger C,
and a human arbiter for unresolved high-stakes concerns. Self-review is not
review; agreement between agents is not evidence.

## When to use

- User asks for an adversarial, security, or deep review / red-team pass over
  code, a diff, or a PR.
- Work is about to be called done or merged — a rubber-stamp risk.
- The repo contains `ADVERSARIAL_REVIEW.md` — its rules are binding.

## Protocol

1. **Repo rules win — if they are current.** If the repo root has
   `ADVERSARIAL_REVIEW.md`, read its `Version` line. At v1.1.0 or newer, follow
   IT exactly — its repo rules, extra lenses, required commands, and role
   labels override this skill's defaults. At any older version, do **not**
   follow it silently: tell the operator it lacks the challenger rule and give
   them the upgrade command from the standard's README; use the bundled v1.1
   protocol until the file is upgraded.
2. **Otherwise use the bundled default protocol** in `default-protocol.md`
   (next to this file): the standard's v1.1.x protocol — one pass per lens,
   evidence rule, severity taxonomy, per-lens verdicts, and the reviewer–
   challenger loop.
3. **Spawn R and C.** You are M. Delegate an independent reviewer pass, then a
   challenger pass that audits R's findings rather than re-reviewing the
   change. Use different model families where available; if not, record the
   limitation. Keep the artifact frozen until the review stabilizes (five
   rounds max), then edit.
4. **One lens at a time.** Each lens is an independent pass; never blend them.
   Every lens ends COVERED or SKIPPED with reason — a silent skip fails the
   review. Read the actual diff and surrounding code, not your own summary.
5. **Typed disagreement.** C returns ACCEPT / REJECT / MODIFY / NEEDS_EVIDENCE
   per finding id and labels objections `DISAGREE_EVIDENCE: <code citation>`
   or `DISAGREE_CONCERN: <epistemic objection>`. A confident rebuttal with no
   code citation cannot settle a concern.
6. **Evidence rule.** Every finding names a concrete trigger you produced: a
   command run, a code path read, a reproduction, with confidence and what
   would change your mind. Unconfirmed suspicions go to UNVERIFIED — never
   dropped, never promoted to findings.
7. **Reconcile, then fix forward.** Record the disposition of every C verdict:
   accept and fix, or reject with reason and evidence. Escalate unresolved
   concerns on CRITICAL/HIGH findings to a human arbiter/NCO. Fix CRITICAL/
   HIGH first, re-review the diff after fixes, and stay in scope.

## Quick reference

| Role | Job |
|------|-----|
| M | Author/editor; no edits during the inner loop |
| R | Propose findings per lens, with severity and confidence |
| C | Dispute findings, check severities, hunt misses; different model family where available |

| Lens | Hunts for |
|------|-----------|
| security | Injection, authn/authz gaps, secret handling, path traversal, tamper-evasion |
| correctness | Edge cases, error paths, races, off-by-one, broken invariants |
| spec-compliance | Divergence from documented contract; tests that don't test what they claim |
| performance | Complexity regressions, unbounded growth, hot allocation paths, latency, bloat |
| abuse | Feature used against intent: spam, forgery, downgrade, replay, griefing |
| operational | Deploy/migration hazards, observability gaps, rollback story |
| governance | Signoffs enforced, verified commits, issues-before-PRs, license hygiene, rule changes |

| Severity | Definition |
|----------|------------|
| CRITICAL | Exploitable security hole, data loss, or broken core guarantee; must fix before merge |
| HIGH | Real defect or guarantee weakened under plausible conditions |
| MEDIUM | Correctness/robustness gap with a concrete failure mode |
| LOW | Hardening, clarity, or hygiene |

Per-lens verdict: COVERED (findings, or what was exercised and found clean —
paths read, commands run) or SKIPPED (reason recorded). Bare "clean" is not
a verdict.

## Common mistakes

- Rubber-stamping: confirming the code works instead of hunting how it fails.
- Reviewing your own summary instead of the artifact.
- Skipping C, or using the same model family for R and C without recording it.
- False consensus: C agreeing with weak findings, or yielding to a confident
  rebuttal with no code citation.
- Findings without reproducible evidence ("looks vulnerable").
- Blending lenses into one pass, or silently skipping a lens.
- Inflating or under-rating severity.
- Silent drops in reconciliation; out-of-scope fixes introduced as "improvements".
- Declaring done with CRITICAL/HIGH findings open, or fixing them without a
  re-review of the diff.

## Output

Write the report to a file — default `docs/adversarial-review-<YYYY-MM-DD>.md`
or as directed — in the standard's format: header (scope, date, M/R/C and
model families); numbered findings (`[SEVERITY] lens — id — path:line — what
breaks — evidence — confidence — would-change-mind — fix`); UNVERIFIED
section; Reconciliation section; per-lens verdict; what was not checked.
