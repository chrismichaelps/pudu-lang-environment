---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0003 — Expansion sees earlier assignments, once

## Context

`$NAME` and `${NAME}` in a value refer to another variable. If a referenced value were expanded
again when used, a value meant literally — `'$HOME'` in single quotes, or `\$HOME` — could be
re-read as a reference, and two values could refer to each other without end.

## Decision

A value is expanded once, when it is assigned, from the values assigned before it (in this file or
an earlier layer) and then the process environment. Without overwriting, the non-empty process
variables count as assigned before every file, because the load keeps them. What a reference answers is final text; it is
never expanded again. A default (`${NAME:-fallback}`, `${NAME-fallback}`) is expanded the same
way, and it is strictly shorter than the text it came from, so expansion always ends.

## Consequences

- A literal `$` stays literal wherever the value is used.
- No cycle can form, so there is no cycle refusal to report.
- A reference to a key assigned later in the file answers the process value or nothing.

## Rejected

- Expanding referenced values recursively with a stack of names in progress: it needs a cycle
  refusal and turns a literal `$` back into a reference.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[domain/Expansion]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment/Domain/Expander]]
