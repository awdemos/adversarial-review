# adversarial-review.com

Repo for the **ADVERSARIAL_REVIEW.md** standard — a repo-declared convention,
in the spirit of `AGENTS.md` / `CLAUDE.md`, for agent-driven adversarial code
review: evidence-backed findings, honest severities, per-lens verdicts.

- [index.html](./index.html) — the landing page (served via GitHub Pages)
- [ADVERSARIAL_REVIEW.md](./ADVERSARIAL_REVIEW.md) — the canonical template you copy into your repo
- [CONTRIBUTING.md](./CONTRIBUTING.md) — governance: issues before PRs, verified (signed) commits, NCO signoff
- [CHANGELOG.md](./CHANGELOG.md) — semver policy and release history
- [docs/review-2026-10-17.md](./docs/review-2026-10-17.md) — the 347-agent adversarial review of v1.0.0 that shaped v1.0.1

License: MIT (see [LICENSE](./LICENSE)).

Quickstart:

```sh
curl -O https://raw.githubusercontent.com/awdemos/adversarial-review/v1.0.1/ADVERSARIAL_REVIEW.md
```

## Install the skill

Coding agents (Claude Code, Kimi Code, Codex, …) can run the whole protocol
from an installable skill:

```sh
git clone https://github.com/awdemos/adversarial-review.git
cp -r adversarial-review/skill/adversarial-review ~/.claude/skills/   # or ~/.kimi-code/skills/, etc.
```

Then just say **"run an adversarial review"** in any project. The skill finds
your repo's `ADVERSARIAL_REVIEW.md` and follows it; without one, it falls
back to the bundled v1.x default protocol ([skill/adversarial-review/SKILL.md](./skill/adversarial-review/SKILL.md)).

## For AI agents installing this standard

You are an AI coding agent reading this README. To adopt the standard in the
repository you are working on, do exactly this, in order:

1. **Fetch the pinned template** (never edit the URL's tag; upgrade versions
   deliberately, not silently):
   ```sh
   curl -fsSL -o ADVERSARIAL_REVIEW.md https://raw.githubusercontent.com/awdemos/adversarial-review/v1.0.1/ADVERSARIAL_REVIEW.md
   ```
2. **Place it at the repository root**, next to `AGENTS.md` / `CLAUDE.md` /
   `README.md`. Do not nest it in a docs or config folder; the convention is
   root-level discovery.
3. **Fill in the `## Repo-specific rules` section.** Never leave its bullets
   empty, and never invent invariants — derive them from this repo's actual
   code, tests, and docs. Each bullet must name something checkable (a
   command, a path, a threshold), not a platitude.
4. **Do not modify anything above that section.** The protocol rules, lenses,
   severity definitions, and output format are the portable standard;
   weakening them voids the contract.
5. **Report what you did**: state the template version installed, the
   repo-specific rules you wrote, and anything you could not determine
   (mark it UNVERIFIED, per the protocol, rather than guessing).
6. **Offer the required follow-up**: after any non-trivial change in this
   repo from now on, run an adversarial review per the installed file —
   independent passes, one lens at a time, reproducible evidence per
   finding, per-lens verdict — before declaring the work done.

If this repository already contains an `ADVERSARIAL_REVIEW.md`, you are done
installing; read it and obey it instead of fetching a new copy.

NCO of record: Andrew White.
