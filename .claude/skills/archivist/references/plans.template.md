<!--
Template for services/<service>/deltas/<slice>.plan.md — the step-by-step
implementation roadmap for realizing a slice's spec and/or design (or a
bounded piece of work that doesn't need a full slice yet). Unlike spec.md/
design.md, a plan is NOT living truth forever: once every step is done and
the outcome is reflected in the code and in those living-contract files, the
plan has served its purpose — it can be left frozen or deleted, it does not
need to be kept in sync after the fact.
Fill every section; delete guidance comments before presenting the draft.
Ground every step in the actual codebase and in the slice's current
spec.md/design.md — never write this from a general implementation guess.
-->

# <Slice or workstream name> — Plan

> <One line: what change this plan implements, and which spec/design it realizes, if any.>

## Status

<!-- not started / in progress / done — update this in place as steps complete; don't append a log entry per update, there's no history layer backing this file. -->

## Goal

<!-- What this plan achieves. Tie it to the spec's contract/invariants and/or the design's states/interactions it's realizing if either exists; otherwise state the concrete outcome directly. -->

## Approach

<!-- Short paragraph: the overall strategy before the step breakdown — why this sequencing, and what alternative was considered and rejected, if that's useful to whoever executes or reviews this. -->

## Steps

<!--
Ordered. Each step should be concrete enough that "done" is unambiguous.
Prefer a table when steps map cleanly to areas/components touched.
-->

| # | Step | Touches | Done when |
|---|---|---|---|
| 1 | <what to do> | <area/component, not a specific file path> | <observable condition> |

## Risks & rollback

<!-- What could break, how you'd notice, how to revert if a step goes wrong. Skip only if this is genuinely low-risk, mechanical work — say so explicitly rather than leaving the section silently empty. -->

## Validation

<!-- How to confirm each step, or the plan as a whole, actually worked — specific tests, a manual check, or both. If it can't be falsified by reading the resulting code/behavior, it isn't specific enough yet. -->

## Out of scope

<!-- What this plan deliberately doesn't attempt. Point back to the spec's and/or design's own Scope/Deferred sections rather than repeating them if either already exists. -->