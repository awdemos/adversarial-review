---
name: adversarial-review
description: Use when the user asks for an adversarial review, security review, deep review, or red-team pass over code, a diff, or a PR; when work is about to be called done or merged and might be rubber-stamped; or when the repo contains an ADVERSARIAL_REVIEW.md file declaring this standard.
---

# Adversarial Review

## Overview

Review as an adversary, not a linter: assume the code is guilty until proven
innocent; every finding must prove itself with reproducible evidence.
Implements the ADVERSARIAL_REVIEW.md open standard (v1.x).

## When to use

- User asks for an adversarial, security, or deep review / red-team pass over
  code, a diff, or a PR.
- Work is about to be called done or merged — a rubber-stamp risk.
- The repo contains `ADVERSARIAL_REVIEW.md` — its rules are binding.

## Protocol

1. **Repo rules win.** If the repo root has `ADVERSARIAL_REVIEW.md`, follow IT
   exactly — its repo rules, extra lenses, and required commands override this
   skill's defaults.
2. **Otherwise use the bundled default protocol** in `default-protocol.md`
   (next to this file): the standard's v1.x protocol — all core lenses,
   evidence rule, severity taxonomy, one pass per lens, per-lens verdicts.
3. **One lens at a time.** Each lens is an independent pass; never blend them.
   Every lens ends COVERED or SKIPPED with reason — a silent skip fails the
   review.
4. **Evidence rule.** Every finding names a concrete trigger you produced: a
   command run, a code path read, a reproduction. Unconfirmed suspicions go to
   UNVERIFIED — never dropped, never promoted to findings.
5. **Fix-forward only after review.** Fix CRITICAL/HIGH findings first, then
   re-review the diff before declaring done.

## Quick reference

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
- Findings without reproducible evidence ("looks vulnerable").
- Blending lenses into one pass, or silently skipping a lens.
- Inflating or under-rating severity.
- Declaring done with CRITICAL/HIGH findings open, or fixing them without a
  re-review of the diff.

## Output

Write the report to a file — default `docs/adversarial-review-<YYYY-MM-DD>.md`
or as directed — in the standard's format: header (scope, date, reviewer);
numbered findings (`[SEVERITY] lens — path:line — what breaks — evidence —
fix`); UNVERIFIED section; per-lens verdict; what was not checked.
