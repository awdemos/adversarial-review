# Contributing to ADVERSARIAL_REVIEW.md

A standard about review discipline owes its own contributors the same
discipline. Three hard requirements, none negotiable:

## 1. Issues before PRs

Every PR must reference an issue that was opened and accepted first. An
unsolicited PR with no linked issue will be closed without review. Use the
issue to argue scope before anyone writes code; use the PR to review what
was actually written. Drive-by fixes for obvious typos may open an issue and
PR simultaneously, but the issue link is still mandatory.

## 2. Verified commits

All commits on `main` must be cryptographically verified:

- Sign commits with **GPG** (`git commit -S`) or **SSH** (`git commit -S --gpg-format=ssh`),
  with the signing key registered on your GitHub account.
- The repo requires verified signatures on the protected `main` branch.
  Unsigned or unverifiable commits are rejected by the merge queue.
- Agents and humans are held to the same bar: an agent-authored commit is
  committed under the human operator's identity and key, and the operator
  takes responsibility for it. No anonymous or keyless automation commits
  to `main`, ever.

## 3. NCO signoff

Every merge to `main` requires an **NCO signoff**: a named
non-commissioned officer of record — the designated senior technical
reviewer for this repository — who did not author the change, independently
re-reviewed it against this standard's own `ADVERSARIAL_REVIEW.md`, and
accepts responsibility for it.

- The signoff is a PR comment of the form
  `NCO signoff: <name> — reviewed <commit range> against ADVERSARIAL_REVIEW.md`.
- Review by the author, or by tooling controlled by the author, does not
  count. Independence is the point.
- Merges without a signoff comment are reverted.
- The current NCO of record is listed in the README. There must always be
  exactly one acting NCO; handoff happens by PR with the outgoing NCO
  signing off the incoming one (the one exception to the independence rule).

## Changing the standard itself

Changes to `ADVERSARIAL_REVIEW.md` (the protocol or core lenses) get the
strictest treatment: the PR description must include a completed adversarial
review of the diff, run against the standard's own rules, with at least one
lens review performed by a party other than the author. Version bumps follow
the CHANGELOG and semver policy there.

## Review expectations

Reviewers apply the repo's own `ADVERSARIAL_REVIEW.md`: reproducible
evidence for claims, honest severities, per-lens verdicts. Findings that
cannot be reproduced are downgraded to UNVERIFIED, not argued over.
