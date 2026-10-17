# ADVERSARIAL_REVIEW.md — Agent Adversarial Review Standard

Version 1.0.0 · https://adversarial-review.com

## What this file is

This file is a machine-readable contract between a repository and any coding
agent (AI or human) that works on it. Just as `AGENTS.md` tells an agent how
to behave in this repo, `ADVERSARIAL_REVIEW.md` tells the agent that its
output is expected to survive **adversarial review** — and defines how that
review is performed, what it hunts for, and how findings are reported.

Drop this file in a repository root (alongside `AGENTS.md` / `CLAUDE.md`).
Agents that support the convention pick it up automatically. Modify the
`## Repo-specific rules` section for your project; the protocol below is the
portable standard.

## The protocol

When asked for an adversarial review (or when finishing a non-trivial change
in a repo that declares this file), the agent MUST:

1. **Review as an adversary, not a linter.** Assume the code is guilty until
   proven innocent. The goal is to find ways the change fails, breaks
   invariants, or can be abused — not to confirm it works.
2. **Run independent passes, one lens at a time.** A complete review covers
   every applicable lens in `## Lenses` below. Each lens is a separate pass;
   do not blend them.
3. **Verify before claiming.** Every finding must be backed by evidence the
   agent actually produced: a command that was run, a code path that was
   read, an experiment result. If a suspicion could not be confirmed or
   refuted, report it as UNVERIFIED — never as a finding.
4. **Rate severity honestly.**
   - **CRITICAL** — exploitable security hole, data loss, or broken core
     guarantee; must fix before merge.
   - **HIGH** — real defect or guarantee weakened under plausible conditions.
   - **MEDIUM** — correctness/robustness gap with a concrete failure mode.
   - **LOW** — hardening, clarity, or hygiene.
   Inflate nothing. A reviewer who cries wolf trains maintainers to ignore
   the report.
5. **Report in the standard format.** Findings are a numbered list, each
   with: severity, lens, `path:line`, what breaks, the evidence, and a
   specific suggested fix. End with an explicit verdict: what was checked
   and found clean, stated per lens — not a vague "no other issues found".
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

Remove lenses that make no sense for your repo; add repo-specific lenses in
the section below.

## Output format

```markdown
## Adversarial review — <scope> (<date>)

1. [CRITICAL] security — src/auth.rs:88 — session token accepted after
   logout. Evidence: `curl` reproduced in 2 requests (log attached).
   Fix: check revocation list in middleware, not only at issue time.
2. [LOW] performance — src/parse.rs:40 — O(n²) fold on startup...
   Fix: precompute once, store by reference.

Verdict per lens: security (1 finding), correctness (clean: 212 paths read,
error branches exercised via X), spec-compliance (1 doc drift), ...
```

## Repo-specific rules

<!-- Edit everything below this line for your project. -->

- Priority invariants that must never regress:
- Known weak spots that always get a second pass:
- Extra lenses or out-of-scope areas:
- Commands the reviewer must run before reporting:
