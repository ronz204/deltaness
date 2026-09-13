<!--
Template for .cursor/skills/<name>/SKILL.md — a capability the agent pulls in
across tasks, or the user invokes explicitly via /<name>. Fill every section;
delete guidance comments before presenting the draft. Keep the body under
~500 lines — split stable reference material into references/*.md and point
to it instead of inlining. `name` must match the folder name (lowercase,
letters/numbers/hyphens only). Optional extra frontmatter fields Cursor
supports: `paths` (glob-scope the skill to matching files),
`disable-model-invocation: true` (only fires via explicit /<name>),
`icon`/`color` (Custom Mode badge), `metadata` — add only if genuinely useful.
-->
---
name: <kebab-case-name>
description: <State what the skill does AND when to use it. Lean slightly pushy on trigger phrasing — skills tend to under-trigger on a vague description, so spell out concrete phrasings/contexts explicitly.>
---

# <Name>

<!-- One short paragraph: what makes this skill different from doing the task ad hoc, and what its output/end-state actually is. -->

---

## Workflow

### 1. <First step>

<!-- Imperative voice, explain why not just what. -->

### 2. <Next step>

---

## Non-goals

<!-- What NOT to do even though it might seem in-scope — the thing that keeps this skill from scope-creeping into a different one. -->