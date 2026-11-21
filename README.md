# adversarial-review.com

Repo for the **ADVERSARIAL_REVIEW.md** standard — a repo-declared convention,
in the spirit of `AGENTS.md` / `CLAUDE.md`, for agent-driven adversarial code
review: evidence-backed findings, honest severities, per-lens verdicts.

This repo also hosts **Loops and Graphs**, a companion control framework that
tells agents how to plan and execute non-trivial work so humans only approve
the final step.

- [index.html](./index.html) — the landing page (served via GitHub Pages)
- [ADVERSARIAL_REVIEW.md](./ADVERSARIAL_REVIEW.md) — the canonical adversarial review template you copy into your repo
- [AGENTS.md](./AGENTS.md) — the Loops and Graphs agent control guide
- [loops-and-graphs/index.html](./loops-and-graphs/index.html) — the Loops and Graphs landing page
- [CONTRIBUTING.md](./CONTRIBUTING.md) — governance: issues before PRs, verified (signed) commits, NCO signoff
- [CHANGELOG.md](./CHANGELOG.md) — semver policy and release history
- [docs/review-2026-10-17.md](./docs/review-2026-10-17.md) — the 347-agent adversarial review of v1.0.0 that shaped v1.0.1

License: MIT (see [LICENSE](./LICENSE)).

## Quickstart: adversarial review

```sh
curl -O https://raw.githubusercontent.com/awdemos/adversarial-review/v1.0.1/ADVERSARIAL_REVIEW.md
```

## Quickstart: Loops and Graphs

```sh
curl -fsSL -O https://raw.githubusercontent.com/awdemos/adversarial-review/main/AGENTS.md
```

## Install the skills

Coding agents (Claude Code, Kimi Code, Codex, …) can run either framework
from installable skills:

```sh
git clone https://github.com/awdemos/adversarial-review.git
cp -r adversarial-review/skill/adversarial-review ~/.claude/skills/      # or ~/.kimi-code/skills/, etc.
cp -r adversarial-review/skill/agent-control-guide ~/.claude/skills/    # or ~/.kimi-code/skills/, etc.
```

Then say **"run an adversarial review"** to exercise the review protocol,
or **"run this with loops and graphs"** to structure execution as a graph
of checked nodes. The `adversarial-review` skill finds your repo's
`ADVERSARIAL_REVIEW.md`; without one, it falls back to the bundled v1.x
default protocol ([skill/adversarial-review/SKILL.md](./skill/adversarial-review/SKILL.md)).
The `agent-control-guide` skill follows the `AGENTS.md` Loops and Graphs
protocol ([skill/agent-control-guide/SKILL.md](./skill/agent-control-guide/SKILL.md)).

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

## For AI agents installing Loops and Graphs

1. **Fetch the pinned guide:**
   ```sh
   curl -fsSL -o AGENTS.md https://raw.githubusercontent.com/awdemos/adversarial-review/main/AGENTS.md
   ```
2. **Place it at the repository root**, next to `ADVERSARIAL_REVIEW.md` /
   `CLAUDE.md` / `README.md`.
3. **Follow the protocol** when planning non-trivial work: write the check
   first, split by blast radius, draw the graph, loop inside every node,
   return the unit not the batch, and gate on reversibility.
4. **Report what the graph looks like:** state the splitter brief, nodes,
   edges, checks, gate rules, and any learning edges you derive.

If this repository already contains an `AGENTS.md` derived from Loops and
Graphs, read it and obey it instead of fetching a new copy.

NCO of record: Andrew White.
