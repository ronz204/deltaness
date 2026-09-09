<!--
This file ships as part of the harness itself, as the fill-in skeleton for
this doc category — copy it into a project as-is, then fill it in place
when bootstrapping (surveyor) or updating (archivist) that project's
per-component functional reference. Delete these guidance comments once
every section holds real, grounded content. Ground every claim in the real
source implementing each component (Step 0) — never infer behavior from a
filename or from what a similar project usually does.
-->

# Modules

<!-- One sentence: what this file covers and what it deliberately excludes (e.g. "Why these components exist lives in overview.md, not here"). -->

---

## `<Component name>`

<!-- Repeat this subsection per component. Component names are durable architecture vocabulary — describe the boundary, not the source-tree location that implements it. -->

**Purpose.** <!-- What this component is responsible for, in one or two sentences. -->

**Flow.** <!-- The actual step-by-step behavior — a numbered list or a short sequence, whichever reads clearer. Describe what really happens, not an idealized version. -->

**Data shape.** <!-- The concrete shape this component reads/writes/exposes. Use a fenced code block for anything structural — never describe a schema or payload in prose when a code block says it exactly. -->

**Dependencies.** <!-- Optional. What this component depends on or is depended on by, only where that detail isn't already obvious from structure.md's system-wide Topology section — component-specific dependency nuance, not a restatement of the global topology. Omit when Topology already covers it. -->

## `<Next component name>`

---

## Non-goals

<!-- Only include this section if a scope boundary here is easy to violate by accident. Omit entirely otherwise. -->
