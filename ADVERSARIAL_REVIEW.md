# ADVERSARIAL_REVIEW.md — Agent Adversarial Review Standard

Version 1.0.1 · Canonical source: https://github.com/awdemos/adversarial-review
· Site: https://adversarial-review.com
· License: MIT (https://github.com/awdemos/adversarial-review/blob/main/LICENSE) —
  copy this file into your repo, modify it, and ship it under your own terms;
  the MIT license applies to the template text.

## What this file is

This file is an agent-readable contract between a repository and any coding
agent (AI or human) that works on it. Just as `AGENTS.md` tells an agent how
to behave in this repo, `ADVERSARIAL_REVIEW.md` tells the agent that its
output is expected to survive **adversarial review** — and defines how that
review is performed, what it hunts for, and how findings are reported.

Drop this file in a repository root (alongside `AGENTS.md` / `CLAUDE.md`).
Agents that support the convention pick it up automatically. Modify only the
`## Repo-specific rules` section for your project; the protocol and lenses
below are the portable standard and must not be weakened (you may add lenses,
not remove the six core ones).

## The protocol

When asked for an adversarial review (or when finishing a non-trivial change
in a repo that declares this file), the agent MUST:

1. **Review as an adversary, not a linter.** Assume the code is guilty until
   proven innocent. The goal is to find ways the change fails, breaks
   invariants, or can be abused — not to confirm it works. Every finding
   must name a concrete trigger: a code path, a command, or a reproduction.
   Hypothetical failure scenarios with no trigger are UNVERIFIED items, not
   findings.
2. **Run independent passes, one lens at a time.** A complete review covers
   every lens in `## Lenses` below. Each lens is a separate pass; do not
   blend them. The verdict must list each core lens as COVERED or SKIPPED
   with a one-line justification. Skipping a lens requires a reason;
   skipping silently is a failed review.
3. **Verify before claiming.** Every finding must be backed by evidence the
   agent actually produced: a command that was run, a code path that was
   read, an experiment result. If a suspicion could not be confirmed or
   refuted, report it under UNVERIFIED with what was attempted and why it
   could not be settled — never promote it to a finding, and never drop it.
4. **Rate severity honestly.**
   - **CRITICAL** — exploitable security hole, data loss, or broken core
     guarantee; must fix before merge.
   - **HIGH** — real defect or guarantee weakened under plausible conditions.
   - **MEDIUM** — correctness/robustness gap with a concrete failure mode.
   - **LOW** — hardening, clarity, or hygiene.
   Inflate nothing. A reviewer who cries wolf trains maintainers to ignore
   the report; a reviewer who under-rates trains them to skip the report.
5. **Report in the standard format.** Findings are a numbered list, each
   with: severity, lens, `path:line`, what breaks, the evidence (a
   reproducible command or transcript, not "(log attached)"), and a
   specific suggested fix. End with an explicit verdict: per lens, either
   the findings or what was exercised and found clean (paths read, commands
   run) — a bare "clean" claim is not a verdict. State the review scope
   (commits, files, or diff reviewed) and anything deliberately not checked.
6. **Fix-forward only after review.** Address CRITICAL/HIGH findings first;
   re-review the diff after fixes. A review of unreviewed new code is not
   done.

## Lenses

| Lens | Hunts for |
|------|-----------|
| `security` | Injection, authn/authz gaps, secret handling, unsafe deserialization, dependency confusion, path traversal, tamper-evasion of integrity guarantees |
| `correctness` | Edge cases, error paths, race conditions, off-by-one, broken invariants, unhandled states |
| `spec-compliance` | Behavior that diverges from documented contract/README; tests that don't test what they claim |
| `performance` | Complexity regressions, unbounded growth, allocation hot paths, startup/latency cost, binary/dependency bloat |
| `abuse` | Ways the feature can be used against its intent: spam, forgery, downgrade, replay, truncation, griefing |
| `operational` | Deploy/persistence failure modes, migration hazards, observability gaps, rollback story |
| `governance` | Contributor-process integrity: signoff requirements actually enforced, verified/signed commits, issues-before-PRs, license and version hygiene, who may change the rules |

## Output format

```markdown
## Adversarial review — <scope: commit range / files> (<date>)

Reviewer: <name or agent id> · reviewing commit <hash>

1. [CRITICAL] security — src/auth.rs:88 — session token accepted after
   logout. Evidence: `curl -X POST ...` reproduced in 2 requests; transcript
   below. Fix: check revocation list in middleware, not only at issue time.
2. [MEDIUM] performance — src/parse.rs:40 — O(n^2) fold on startup; measured
   42 ms per call at n=15k. Evidence: benchmark command and output below.
   Fix: precompute once, store by reference.

UNVERIFIED: suspected race in src/queue.rs:120 — attempted reproduction with
`stress -c 8` for 10 min, inconclusive. Needs a machine with >8 cores.

Verdict per lens: security COVERED (1 finding), correctness COVERED (clean:
212 paths read, error branches exercised via test X), performance COVERED
(1 finding), abuse SKIPPED (no externally reachable surface in this diff) —
reason recorded, spec-compliance COVERED (1 doc drift), operational COVERED
(clean: deploy path re-run in staging), governance COVERED (clean).
Not checked: Windows builds (out of scope for this diff).
```

## Repo-specific rules

<!-- Edit everything below this line for your project. -->

The canonical repo (awdemos/adversarial-review) fills these in as the worked
example; every adopter MUST fill them in too — an empty section means the
protocol's evidence rules have nothing to enforce.

- Priority invariants that must never regress: the six protocol rules and
  six core lenses; the quickstart command must always work; this file's
  governance requirements.
- Known weak spots that always get a second pass: the output-format example
  (it models behavior every reviewer copies); severity definitions (drift
  here redefines every future report).
- Extra lenses or out-of-scope areas: none; `web` lens applies to index.html
  (a11y, meta/OG tags, factual claims).
- Commands the reviewer must run before reporting: execute the quickstart
  curl command and confirm the fetched file parses; render index.html and
  check the linked template resolves.
