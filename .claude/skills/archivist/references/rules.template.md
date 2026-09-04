<!--
Template for .claude/rules/<name>.md — a convention auto-loaded when a matching
file is opened/edited. Fill every section; delete guidance comments before
presenting the draft.
-->
---
paths:
  - "<glob pattern, quoted — YAML requires it for patterns starting with * or {>"
---

# <Name> Conventions

<!--
One short paragraph: what this covers, what runtime/toolchain it targets, and
what's explicitly out of scope for now (library-specific conventions not yet
decided, forward-looking vs. descriptive-of-current-code, etc). Omit the
`paths:` frontmatter entirely only if this rule is meant to be global (rare —
today only coding.md is global).
-->

---

## <Topic 1>

<!-- Bullet list. Each bullet states the convention AND the reason ("X, because Y") — a bare "must" invites being silently violated the first time it's inconvenient. -->

## <Topic 2>

---

## Non-goals

<!-- Optional. Include only if this rule is easy to over-apply — e.g. a convention that shouldn't be retrofitted onto an explicitly-excepted case. -->