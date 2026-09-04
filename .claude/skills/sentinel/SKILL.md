---
name: sentinel
description: Use to verify that a slice's <slice>.spec.md invariants, <slice>.design.md claims, and <slice>.plan.md status still hold against the real implementation — a check, not a rewrite. Trigger when the user asks to verify/audit/check a slice, wants to know if a spec or design is still accurate, wants confirmation that a plan's steps actually landed, or has just finished implementation work on a slice that has a spec, design, or plan — e.g. "¿sigue vigente este spec?", "¿esta pantalla sigue cumpliendo su design.md?", "verifica que el plan ya se cumplió", "¿la implementación sigue cumpliendo los invariantes de X?", "check this slice for drift", "audita el plan de Y". Defaults to a narrow scope (the named slice, or the slice(s) touched by the current diff/recent changes) and never sweeps every spec in the project unasked — token cost for real verification is real, and this skill is designed around bounding it. Reports findings; never edits spec.md, design.md, plan.md, or source itself — corrections go through `archivist` (and, for a genuine spec/design-vs-code conflict, the same reconciliation discipline `specifier` uses).
---

# Sentinel

`archivist` writes. `specifier` interviews to produce what `archivist` writes. `sentinel` checks whether what's already written is still true — its reason to change is "does the implementation still match its contract," never "is the artifact shaped correctly" or "did we ask the right questions." Same portability rule as its siblings: nothing below assumes a specific stack, and it doesn't check anything outside `<slice>.spec.md` / `<slice>.design.md` / `<slice>.plan.md` — no doc/rule drift-checking, that's a different, broader job this skill deliberately doesn't take on. A spec's or design's optional Context section is out of scope too, deliberately: it's free-form theory, not a set of falsifiable claims, so there's nothing for this skill to check it against.

Real verification means reading real code, not just re-reading a spec — that's inherently more expensive than writing one. The steps below are built around keeping that cost bounded on purpose: narrow scope by default (Step 0), targeted checks before full-file reads (Step 2), and pushing the expensive reading into disposable subagent contexts instead of the main session (Step 2). Skipping these isn't a shortcut, it's the trade-off this skill exists to avoid making.

---

## Step 0 — Scope before anything else

This is the main cost lever — get it narrow before doing any reading.

- **If the user names a slice**, scope is every file that slice name resolves to across the whole repo: `<slice>.spec.md`, `<slice>.design.md`, and `<slice>.plan.md` wherever each exists. Nothing wider than that slice, but check every service's `deltas/` for it, not just one. In a repo split across multiple services, a slice's files don't have to share a directory — `design.md` lives under whichever service renders the UI, and each service can even carry its own `<slice>.spec.md` scoped to the logic it owns there (e.g. a backend's session/data contract and a frontend's own navigation-logic contract, same slice name, deliberately different content) — treat those as siblings to check together, not duplicates to collapse into one.
- **If the user doesn't name one** ("check what I just did"), use `git status`/`git diff` to find changed files, map them back to the slice(s) they belong to, and confirm with the user if more than one slice plausibly matches rather than guessing silently.
- **If the user explicitly asks for everything** ("revisa todos los specs"), honor it — but first correlate by slice name across every service's `deltas/`, so a slice with siblings in two services is checked and reported as one slice, not two unrelated hits. Say up front that this dispatches one check per slice (not per file) and will cost proportionally, and offer to narrow first. Don't refuse, just don't default to it.
- **If a slice has no `<slice>.spec.md`**, there's nothing to verify against — say so and stop; suggest `specifier`/`archivist` if the user wants one written. Don't invent invariants to check from the implementation alone. Same rule for `<slice>.design.md` if the user specifically asked about the slice's UI and it has none.
- **If a slice has a `spec.md` but no `design.md`**, that's normal — not every slice has a UI-facing surface — just verify what exists and note design verification doesn't apply rather than treating it as a gap. Same for a `spec.md` with no `plan.md`: plans are optional and often deleted once done.

## Step 1 — Read the contract

Read the scoped `<slice>.spec.md` (and `<slice>.design.md`, if in scope) in full — they're short and bounded, this is the cheap part. Pull out:

- Every line in `spec.md`'s **Invariants** (and any hard guarantee stated in its **Contract**) as one falsifiable claim to check.
- Every line in `design.md`'s **States**, **Interactions**, and **Consumes** (and any hard guarantee stated in **Layout**) as one falsifiable claim to check — e.g. "the empty state shows X," "this action requires confirmation before firing," "this field is sourced from `spec.md`'s Y."
- If `<slice>.plan.md` is in scope, read its **Status** and **Steps** table — each step marked done is a second kind of falsifiable claim ("this landed in code"); each step marked not-started/in-progress is a claim too ("this hasn't landed yet").

A claim that isn't falsifiable by reading code/behavior isn't something this skill can check — flag it back to the user as unverifiable-as-written rather than forcing a verdict.

## Step 2 — Verify against the real implementation, cheaply

For each claim:

- **Try a targeted check first.** Grep for the specific mechanism the claim names (a check, a guard, a config value) rather than reading whole files end to end — most invariants are mechanically checkable once you know what pattern to look for.
- **Delegate the expensive reading, don't inline it.** If confirming a claim needs broader reading/judgment (not just a grep hit), dispatch it to a subagent instead of pulling that source into the main session's context:
  - If a matching `.claude/agents/*` subagent already exists for this kind of check (e.g. a spec-invariant-checker scoped to this slice), use it — that's exactly what it's for.
  - Otherwise use the `Agent` tool per slice (or per cluster of related claims), so the reading happens in a disposable context and only the verdict comes back. Run independent slices' checks in parallel rather than sequentially.
  - If this kind of check is going to recur for a slice, say so and suggest the user have `archivist` create a dedicated subagent for it (via its own `agents.template.md`) instead of re-paying full inline cost every time.
- **Never guess a verdict.** If what was actually read doesn't clearly confirm or contradict a claim, the verdict is `Unverified`, not a best guess in either direction.

## Step 3 — Report, don't fix

Report as a compact table, not prose per finding — a verbose report defeats a cost-conscious skill:

| Slice | Source | Claim | Verdict | Evidence |
|---|---|---|---|---|
| `<slice>` | spec / design / plan | `<the claim, restated short>` | Holds / Violated / Stale / Unverified | `<what was actually checked — the pattern found or not found>` |

- **Holds** — confirmed true against the real implementation.
- **Violated** — confirmed false; the implementation contradicts the claim.
- **Stale** — plan-only: the step's status doesn't match what's actually in the code.
- **Unverified** — couldn't be confirmed either way from what was read; say why.

Lead with anything `Violated` or `Stale` — a clean bill of health is the least interesting part of the report.

## Step 4 — Hand off, don't resolve silently

This skill's job ends at the report. What happens next depends on which side is wrong, and that's the user's call, not this skill's to assume:

- **If the code is wrong** (implementation drifted from a still-desired invariant or design claim), that's a bug — say so plainly, don't touch the code.
- **If the spec/design/plan is wrong** (the invariant or design claim no longer applies, or a step's real status differs from what's recorded), hand off to `archivist` to correct the file in place — same as `specifier`'s Step 3, don't pick a side quietly, surface it and let the user confirm which one wins before anything gets edited. If the drift is a `design.md` claim restating something that actually belongs in `spec.md` (or vice versa), flag that shape problem too, not just the factual one.

---

## Non-goals

- Doesn't check `.claude/docs/*` or `.claude/rules/*` for drift — scope is a slice's spec, design, and plan only. A broader drift-checker is a different skill, not a reason to stretch this one.
- Doesn't check a spec's or design's optional Context section — it's free-form theory, not a falsifiable contract, so there's nothing to verify it against.
- Doesn't write or edit `spec.md`, `design.md`, `plan.md`, or source code — verification only, always handed off.
- Doesn't sweep every slice in the project by default — see Step 0.
- Doesn't force a Holds/Violated verdict when the evidence genuinely doesn't settle it — `Unverified` is a legitimate, honest outcome, not a failure to try harder.