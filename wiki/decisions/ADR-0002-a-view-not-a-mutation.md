---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0002 — Loading answers a view, not a mutation

## Context

An environment loader classically writes what it read into the process environment, and the rest
of the program reads it back from there. `Std.Env` reads the process environment and has no way to
change it, and a process-wide write is shared mutable state every thread observes mid-change.

## Decision

`Loader.load` answers [[src/PuduLangEnvironment/Variables]]: the process environment as one
snapshot with the loaded values settled over it by the overwrite rule an export follows — a loaded
value replaces a variable only when overwriting is on or the variable is unset or empty. The
caller holds the value, passes it to what needs configuration, and hands it to child processes
with `Variables.apply`. `Variables.process()` is the same view without any file.

## Consequences

- Loading has no global effect; two loads with different options coexist in one program, and
  tests run in parallel without touching shared state.
- The values live exactly as long as the caller keeps the `Variables` value.
- Code that reads `Std.Env` directly does not see loaded values; it reads the view instead.

## Rejected

- A module-level registry of loaded values: module scope holds only constants, and a hidden global
  is the shared state this decision avoids.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[domain/Variables]] · [[grammar/pudu]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment/Loader]]
