<!--
Template for .claude/agents/<name>.md — a subagent: a bounded, repeatable
task with its own system prompt, its own tool access, and its own context
window. Fill every section; delete guidance comments before presenting the
draft.
-->
---
name: <kebab-case-name>
description: <What this agent does AND when to invoke it — same trigger-specificity discipline as a skill's description. Include a concrete "use after/before X" cue rather than a vague summary.>
tools: <Minimum tool list this task actually needs, e.g. "Read, Glob, Grep" for a review-only agent — don't grant write access it won't use.>
model: <Choose deliberately: a cheaper/faster model for narrow search or lint-style checks, the default model for anything requiring judgment.>
---

<!--
The system prompt (everything below the frontmatter). Ground it in this
project's actual conventions — pull specifics from .claude/rules/ and
.claude/docs/ rather than generic reviewer advice. Same volatile-vs-durable
rule as every other artifact: describe the pattern to check for, never cite
a specific file as the example of it.
-->

You are <role, one sentence — what this agent is responsible for and the boundary of that responsibility>. Check specifically for:
- <concrete, checkable criterion 1>
- <concrete, checkable criterion 2>
- <...>

<!-- State what the agent should do when it finds a violation — flag it, fix it inline, refuse, ask — don't leave the output behavior implicit. -->