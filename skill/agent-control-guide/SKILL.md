---
name: agent-control-guide
description: Use when planning or executing non-trivial agent work, designing autonomous pipelines, or when the user wants to stop babysitting intermediate steps. Enforces the Loops and Graphs control framework from the adversarial-review.com agent control guide.
---

# Agent Control Guide: Loops and Graphs

## When to use

- The user asks for a multi-step implementation, refactor, audit, or review.
- The user wants agents to run autonomously and only approve the final step.
- The user asks how to structure agent work, reduce babysitting, or design checks and gates.
- The repo contains `AGENTS.md` derived from the Loops and Graphs framework.

## Protocol

1. **Write the check first.** Before producing output, define a deterministic condition that can fail the work. Absence of an error is not a check.
2. **Split by blast radius.** Use a splitter node to divide work into bounded units. Do not split by folder or file count alone.
3. **Draw the graph.** Connect two nodes only if the second consumes the first's output. Remove false serialism.
4. **Loop inside every node.** For each unit: produce, check, correct, repeat until green. Cap retries at three.
5. **Use four node types only:** splitter, worker, code node, gate. Prefer code nodes for transformations with one correct answer.
6. **Return the unit, not the batch.** When one unit fails, send only that unit back with unit, verdict, reason, evidence, and scope.
7. **Build two return paths:** the short correction edge back to the worker, and the long learning edge back to the splitter as a constraint.
8. **Gate on reversibility, not confidence.** Open cheap-to-revert lanes first. Never open hard-to-reverse lanes automatically.
9. **Put the human on the merge.** The human approves the highest-consequence, lowest-reversibility step. Do not ask for intermediate approval.

## Decision checklist

Before executing a non-trivial step, answer:

1. What deterministic check can fail this step?
2. What is the blast radius if it is wrong?
3. Is it reversible without data loss or human cleanup?
4. Which single unit owns a failure here?
5. What constraint will an accepted result add to future splits?
6. Is the human being asked only at the highest-consequence step?

## Common mistakes

- Treating "no errors" as evidence of correctness.
- Running independent steps serially because they were typed in order.
- Returning a whole batch for one failing unit.
- Using model confidence as a gate input.
- Building a review step instead of a failing check.
- Asking the human to approve intermediate output.

## Output

When applying this skill, state the graph in plain language before acting:

- Splitter brief: how the work is divided.
- Nodes: what each unit does.
- Edges: which outputs feed which inputs.
- Checks: the failing condition for each node.
- Gate rules: which lanes are open, closed, or human-only.
- Learning edges: what constraints accepted results add.
