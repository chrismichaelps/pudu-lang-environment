---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Expander.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Expander]
---

# PuduLangEnvironment.Domain.Expander

## Purpose

Replaces references to other variables inside a value.

## Interface

### Signatures

```pudu
export fn expand(value: Str, known: &Map[Str, Str], process: &Map[Str, Str]) -> Str
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangEnvironment/Domain/Parser]].

## Algorithm

1. Walk the characters once. A run of backslashes before `$` is halved; an odd run makes the `$`
   literal. A run not before `$` is kept whole.
2. `${…}` with balanced braces is a braced reference; an unbalanced one leaves `$` literal.
3. `$` followed by a letter or `_` reads a name of letters, digits, and `_`.
4. A braced reference splits at the first top-level `:-` or `-` into a trimmed name and a default.
5. A name answers its known value, else its process value, else nothing; `:-` answers the expanded
   default when that is unset or empty, `-` when unset.

## Negative Logic (Prohibited Paths)

- A value answered for a name is never expanded again
  ([[decisions/ADR-0003-expansion-sees-earlier-assignments]]).

## Edge Cases

- `${}` and `${:-x}` answer the default when there is one and nothing otherwise.
- `$1`, `$-`, and a trailing `$` are literal.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangEnvironment/Domain/ExpanderTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why look in known assignments before the process environment?
  **A:** A file that sets `HOST` and then uses `$HOST` means its own `HOST`. _Rejected:_ the process
  first (makes a file's meaning depend on the shell).
- **Q:** Why does the default text expand?
  **A:** `${PORT:-${DEFAULT_PORT}}` is how fallbacks chain. Termination holds because a default is
  strictly shorter than the reference it sits in.

## Referenced by

[[CHANGELOG]] · [[domain/Expansion]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[subsystems/Parsing]]
