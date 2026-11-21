# Agent Control Guide: Loops and Graphs

**Version:** 0.1.0  
**Companion:** [ADVERSARIAL_REVIEW.md](./ADVERSARIAL_REVIEW.md) tells agents how to doubt their output. This file tells them how to plan and execute the work that leads to it.

## What this file is

This is a control guide for coding agents. It tells you how to plan and execute non-trivial work so that a human only has to approve the final step, not babysit every intermediate one.

The core idea: a **loop** makes one unit of work correct without a human, and a **graph** decides which units exist and how they connect. You need both. A graph without loops in its nodes produces unverified work in parallel. A loop without a graph around it is one excellent step in a queue nobody designed.

## The one rule

A task is not done when the output looks right or when no error was raised. A task is done when a **deterministic check that can fail** has passed, the change is bounded to the planned scope, and the remaining risk is reversible or gated by a human.

Absence of an error is not evidence of correctness. Build accordingly.

## How to structure any non-trivial task

1. **Write the check first.** Before producing anything, define what "green" means in a way a program can evaluate.
2. **Split the work.** Use a splitter node to cut the task into bounded units. Split by blast radius, not by folder or file count.
3. **Draw the graph.** Connect units only when one's output is consumed by the next. "And then" is not an edge.
4. **Run independent units in parallel.** If two nodes have no edge between them, do not run them serially.
5. **Loop inside every node.** Produce, check, correct, repeat until green. Cap retries at three per unit.
6. **Gate on evidence, not confidence.** Open the gate when deterministic results, trajectory, and reversibility allow it.
7. **Return failures to the unit that produced them.** Never return the whole batch for one failing unit.
8. **Write learning edges.** Every accepted result becomes a constraint the splitter applies to future runs.
9. **Put the human on the merge.** The human approves the highest-consequence, lowest-reversibility step. Nothing else.

## What counts as a check

A check must be able to fail the work while no human is watching. Good checks look like this:

- GREEN: the test suite exits 0.
- GREEN: the diff touches only files listed in the plan.
- GREEN: every factual claim carries a source line.
- GREEN: the type checker passes with no errors.

These are not checks:

- NOT A CHECK: the output looks good.
- NOT A CHECK: the model says it is confident.
- NOT A CHECK: no errors were raised.

If your check cannot be expressed as a failing condition, rewrite it until it can.

## Node types

Use exactly four node types. Do not invent more.

| Node type | Job | Example |
|-----------|-----|---------|
| **Splitter** | Cuts the work into units and writes the brief for each. | Divide a refactor into slices by blast radius. |
| **Worker** | Does one unit with one lens, in its own context. | Audit one slice for race conditions. |
| **Code node** | Applies a transformation with exactly one correct answer. | Merge, rank, deduplicate, compare exports. |
| **Gate** | Decides whether a unit's output may continue. | Open only if tests pass and scope is clean. |

If you can describe a node without using the words judge, decide, assess, or summarize, it is probably a code node. Use code for those. Model reasoning is for splitting, framing, and gates.

## The two return paths

A graph without a way back is a pipeline. It produces output and forgets. Build two return paths instead.

### Correction edge (short)

A gate rejects one unit back to the worker that produced it. The unit fixes itself inside the current run.

What travels with the return:

- **UNIT:** the failing slice or file.
- **VERDICT:** red.
- **REASON:** the exact check that failed, e.g. `test_auth_redirect` expected 302, got 200.
- **EVIDENCE:** command output, path, line number.
- **SCOPE:** fix this file only; do not touch other slices.

Cap correction at three attempts per unit. If a unit still fails, the problem is in the splitter's plan, not the worker. Escalate to the human.

### Learning edge (long)

An accepted result goes back to the splitter as a constraint, not as raw output. This fixes every run after the current one.

Example:

- ACCEPTED: the `utils` slice ported cleanly.
- DERIVED: adapters must preserve keyword args exactly.
- LANDS IN: the splitter's brief for every later slice.

A confirmed cause becomes a rule. The next run starts where this one ended.

## Return the unit, not the batch

If four slices were ported and one fails its tests, return only the failing slice. If the whole batch goes back, three correct slices get rewritten into different, not better, versions. You then re-verify all four, and any of the three may fail for unrelated reasons. One failure becomes four uncertain outcomes.

Never allow a returned unit to grow. Without an explicit scope, the agent opens adjacent files, notices other issues, and turns a one-slice correction into a multi-file diff nobody reviewed.

## How gates open

Sort work by how expensive the mistake is to undo. Confidence is the weakest input because it is the only one the model can influence.

| Lane | Reversibility | Gate rule |
|------|---------------|-----------|
| Open first | Reversible and contained | Copy changes, tests, isolated functions with coverage. One bad merge costs a revert. |
| Open second | Reversible but wide | Shared utilities, schema additions, callers across many files. Gate on deterministic checks plus a clean trajectory. |
| Never open | Hard to reverse | Migrations, deletions, anything writing production data or moving money. This lane does not open, regardless of confidence score. |

Inside an open lane, the gate reads evidence in this order:

1. Deterministic results (tests, type checks, diff scope).
2. Trajectory of this run (repeated failures, drift, new warnings).
3. Historical rollback rate from this node.
4. The model's own assessment. Last.

## Where to start

Do not build the whole graph at once. Start with one repeated task and draw:

- one splitter,
- two to three lanes,
- one merge point,
- one gate,
- one back edge.

Build the gate first. Everything gets easier once something can fail loudly. Then add lanes. Then add the learning edge last, because you cannot derive constraints from accepted results until something is accepting results.

## Decision checklist

Before you execute a non-trivial step, answer these questions out loud:

1. What is the deterministic check that can fail this step?
2. What is the blast radius if this step is wrong?
3. Is it reversible without data loss or human cleanup?
4. Which single unit owns a failure here?
5. What constraint will an accepted result add to future splits?
6. Is the human being asked to approve only the highest-consequence step?

If you cannot answer question 1, stop and write the check first.

## Three lines that hold the whole discipline

- Measure the path, not only the answer it landed on.
- A verdict that does not change what runs next is a report.
- Any failure you do not turn into a permanent constraint, you will meet again.

## Version and governance

This is version 0.1.0. The rules above are the stable control surface. Add examples, tighten language, or add node subtypes in later versions, but do not weaken the one rule, the four node types, the two return paths, or the gate ordering.

Proposed changes to this file follow the same governance process as ADVERSARIAL_REVIEW.md: issues before pull requests, cryptographically verified commits, and NCO signoff on every merge.
