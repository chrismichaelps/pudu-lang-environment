---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Cascade.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Cascade]
---

# PuduLangEnvironment.Domain.Cascade

## Purpose

The environment name and the layered file names it selects.

## Interface

### Signatures

```pudu
export fn chooseName(explicit: &Option[Str], process: &Map[Str, Str]) -> Option[Str]

export fn isSafe(name: Str) -> Bool

export fn candidates(name: &Option[Str]) -> Array[Str]
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Names]].
- **Consumed by:** [[src/PuduLangEnvironment/Discovery]].

## Algorithm

1. `chooseName`: the explicit name when not blank, else the first naming variable that is set and
   not blank, trimmed.
2. `isSafe`: not blank, no `..`, and no character refused in a file name.
3. `candidates`: `.env`, `.env.local`, then `.env.<name>`, `.env.<name>.local` when named.

## Negative Logic (Prohibited Paths)

- `candidates` is only asked for a name `isSafe` accepted ([[src/PuduLangEnvironment/Discovery]]).

## Edge Cases

- A name with surrounding space is trimmed before it is checked.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangEnvironment/Domain/CascadeTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why refuse the characters every platform refuses, not only this one's?
  **A:** A project's files move between machines; a name valid here and invalid there fails late.
  _Rejected:_ asking the running platform.

## Referenced by

[[CHANGELOG]] · [[domain/Discovery]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Domain/_MOC]]
