---
name: surveyor
description: Use to bootstrap this project's delta knowledge base in a new or existing repo that doesn't have one yet — greenfield or brownfield. Produces CLAUDE.md and the initial .claude/docs/* files in one guided pass by scanning the real repo (or interviewing, if there's nothing to scan yet) and handing the result to archivist to write. Trigger on "inicializa este proyecto con delta", "arranca el harness aquí", "quiero modelar el contexto de este proyecto", "monta la knowledge base", "dale contexto a Claude de este repo", "bootstrap this repo", "set up the delta harness here", "onboard this codebase" — or any request to get a project's context up fast at the start of working on it. Never creates a slice (services/*/deltas/*) — that's specced later, on demand, via specifier. If CLAUDE.md already exists with real content, this isn't the right skill; that's an incremental update through archivist instead.
---

# Surveyor

`specifier` and `sentinel` both assume a knowledge base already exists for the slice they're working on. Day one of a project, it doesn't — there's no `CLAUDE.md` to orient a session, no `.claude/docs/*` to check a spec against, nothing to read before writing. Surveyor exists to close that gap fast: one guided pass that turns a bare repo (or a repo with no delta knowledge base yet) into a working `CLAUDE.md` plus whatever `.claude/docs/*` categories the project actually warrants, instead of that context trickling in one ad hoc `archivist` call at a time over the first several sessions. Like its siblings, this skill is portable — nothing below assumes a specific stack or repo layout.

Same SRP split as `specifier`: this skill investigates and interviews, `archivist` writes. Surveyor's reason to change is "did we get the project oriented fast and accurately" — not "does the artifact land in the right shape," which stays `archivist`'s job alone.

---

## Step 0 — Confirm this is actually a bootstrap

- Check whether `CLAUDE.md` (root or `.claude/`) already exists and has real content. If it does, this isn't a bootstrap — hand off to `archivist` for a normal, targeted update instead of re-running initialization over curated content.
- Check whether `.claude/docs/` already has files. Partial existing content isn't a blocker — treat it the same as Step 0 of every other skill in this family: read what's there first, only fill genuine gaps, don't duplicate or contradict it.
- Determine whether the repo is brownfield (real source, a package manifest/build config, existing structure) or greenfield (empty or skeleton). This decides how Step 1 gathers material — from code, or from the user.

## Step 1 — Investigate wide, not deep

Unlike `specifier` (which goes deep on one slice) or `archivist`'s own Step 0 (deep read of the one artifact being written), this pass is breadth-first across the whole repo — enough to orient, not to exhaustively document every module on day one:

- The package manifest/build/task-runner config and CI config, for the real install/dev/test/build commands — never guess a command that "should" work.
- The top-level directory tree, for what each major part is for.
- Any existing README or doc, for stated purpose/vision — cross-check it against the actual code rather than trusting it as-is.
- Enough of the codebase's shape to judge which `.claude/docs/*` categories from `archivist`'s own artifact table genuinely apply here (vision/overview, functional/module reference, topology/structure, static infrastructure) — skip a category outright if the project doesn't have that kind of content, rather than creating a thin placeholder file for it.
- Natural bounded contexts visible in the repo's structure — note them as slice candidates for Step 4's report, don't investigate any one of them deeply; that depth is `specifier`'s job, later, on demand.
- Cross-cutting conventions the codebase already follows consistently that aren't caught by a linter/type-checker/CI check — rule candidates, per `archivist`'s "Spotting a rule candidate" check (cross-cutting, not tool-enforced, applies every time, would be silently violated otherwise). Note them; don't write them yet, and don't mistake incidental consistency (only happens to look the same so far) for a deliberate convention.
- Evidence relevant to `references/baseline-checklist.md` (auth middleware, input validation, caching, queues, rate limiting, pagination) — enough that Step 2's questions on this can be confirmations of what was found, not blind asks.

For a greenfield repo with nothing to scan, this step is short by necessity — confirm that with the user rather than inventing structure that doesn't exist yet, and lean more on Step 2's interview.

## Step 2 — Ask what the repo alone can't answer

Same discipline as `specifier`: confirm from Step 1 where possible, ask where it isn't — don't guess. In a short, focused batch (`AskUserQuestion` where the choices are concrete):

- **Vision** — what is this project, in a sentence or two of domain language, not a restatement of the directory tree.
- **Scope boundaries** — anything explicitly out of scope for the project as a whole, if that's already decided.
- **Infrastructure** — whether the project has static infrastructure/provisioning worth a `.claude/docs/infrastructure.md`, if Step 1 couldn't tell from config alone.
- For greenfield repos specifically: intended stack and repo layout, since there's no code yet to read it from.
- **Rule candidates** — for each pattern Step 1 flagged, confirm with the user it's a deliberate convention worth enforcing on every future touch of matching files, not just how the code happens to look so far. Drop anything unconfirmed rather than creating it as a guess.
- **Security, performance, and scalability baseline** — walk `references/baseline-checklist.md` in this same batch. Every project has to satisfy these three in some form, even when the honest answer is "not applicable here" — the checklist is a fixed set of questions, not prewritten answers; route each confirmed one per its own routing rules (a `structure.md` note, a rule candidate, or a consideration flagged for a future slice's `spec.md`) rather than writing it down verbatim.

## Step 3 — Hand off to archivist

Package the investigation and interview results and invoke `archivist` to write, in one batch:

- `CLAUDE.md` via `references/mark.template.md` — project description, knowledge base layout, repo layout, setup/common commands, permissions summary, conventions.
- Each warranted `.claude/docs/*.md` via `references/docs.template.md` — only the categories Step 1 actually found material for.
- Each rule candidate confirmed in Step 2, as its own `.claude/rules/<topic>.md` via `references/rules.template.md` — one file per concern, same as any other rule.

This skill never writes any of these itself, same separation `specifier` keeps from `archivist` — it produces grounded material, `archivist` still does the write and still applies its own Step 0 discipline against what was found.

## Step 4 — Report what's next

Close by naming, explicitly:

- What got created, including which rules were written and why each earned one.
- The slice candidates noted in Step 1, as a short list — not specced, just surfaced, so the user knows what exists to spec later via `specifier` when they're ready to work on one.
- Any security/performance/scalability consideration from the baseline checklist that was confirmed but scoped to a specific slice rather than the whole project — flagged here, not written anywhere yet, for `specifier` to fold into that slice's `spec.md` Invariants once it gets specced.

---

## Non-goals

- Never creates a `services/*/deltas/*` slice file — bootstrapping a project's context and specifying one of its slices are different jobs; a slice gets specced later, one at a time, via `specifier`.
- Never creates a `.claude/rules/*.md` without the user confirming the pattern is deliberate and worth enforcing every time — unlike `CLAUDE.md`/docs content, a false-positive rule actively injects wrong guidance into every future session that touches a matching file, not just a passive gap.
- Never overwrites an existing `CLAUDE.md`/`.claude/docs/*` that already has real content without the user confirming a deliberate refresh — see Step 0.
- Doesn't write `CLAUDE.md` or any doc file itself — always hands off to `archivist`.
- Doesn't attempt exhaustive documentation of every module or subsystem in this one pass — a lean, accurate first cut is the goal; depth accrues afterward through normal `archivist` and `specifier` use as the project is actually worked on.
