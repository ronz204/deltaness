<!--
Template for .cursor/rules/<name>.mdc — a convention loaded automatically,
either because the agent judges it relevant (Agent Requested: description
set, no globs) or because a matching file is in context (Auto Attached:
globs set, no description). Fill every section; delete guidance comments
before presenting the draft. The .mdc extension is mandatory — a plain .md
file placed here is ignored by Cursor.
-->
---
description: <One sentence: what this covers and when it's relevant. Required for Agent Requested; omit if using globs instead (Auto Attached), or omit both for a Manual rule invoked via @name.>
globs: <"<comma-separated glob patterns>" — required for Auto Attached, omit for Agent Requested or Manual.>
alwaysApply: false
---

# <Name> Conventions

<!--
One short paragraph: what this covers, what runtime/toolchain it targets, and
what's explicitly out of scope for now (library-specific conventions not yet
decided, forward-looking vs. descriptive-of-current-code, etc).
-->

---

## <Topic 1>

<!-- Bullet list. Each bullet states the convention AND the reason ("X, because Y") — a bare "must" invites being silently violated the first time it's inconvenient. -->

## <Topic 2>

---

## Non-goals

<!-- Optional. Include only if this rule is easy to over-apply — e.g. a convention that shouldn't be retrofitted onto an explicitly-excepted case. -->
