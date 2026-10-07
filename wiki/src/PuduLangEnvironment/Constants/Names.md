---
type: module
path: "@root/src/PuduLangEnvironment/Constants/Names.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.2
depth_status: SHALLOW
tags: [module]
aliases: [PuduLangEnvironment.Constants.Names]
---

# PuduLangEnvironment.Constants.Names

## Purpose

File names, variable names, words, and limits the package agrees on.

## Interface

### Signatures

```pudu
export const DEFAULT_FILE: Str

export const LOCAL_SUFFIX: Str

export const ENVIRONMENT_NAME_VARIABLES: Array[Str]

export const DEFAULT_PROBE_LEVELS: Int

export const BYTE_ORDER_MARK: Str

export const EXPORT_KEYWORD: Str

export const TRUTH_WORDS: Map[Str, Bool]

export const UNSAFE_NAME_CHARACTERS: Set[Char]

export const PARENT_STEP: Str

export const KIND_INT: Str

export const KIND_FLOAT: Str

export const KIND_DECIMAL: Str

export const KIND_BOOL: Str

export const REDACTED: Str

export const SETTING_SEPARATORS: Set[Char]
```

### Linkage

- **Requires:** nothing.
- **Consumed by:** [[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Domain/Keys]], [[src/PuduLangEnvironment/Discovery]], [[src/PuduLangEnvironment/Domain/Cascade]], [[src/PuduLangEnvironment/Domain/Parser]], [[src/PuduLangEnvironment/Domain/Values]], [[src/PuduLangEnvironment/Options]].

## Algorithm

1. `.env` is the default file and the root of every cascade name; `.local` marks a machine's own layer.
2. `PUDU_ENV`, then `PROFILE`, name the environment for the cascade; `PROFILE` is the variable the
   standard library's configuration already reads its profile from.
3. Probing climbs four parents unless told otherwise.
4. The kinds a conversion refusal names, and the placeholder a redacted field renders as.

## Negative Logic (Prohibited Paths)

- The truth words and the characters refused in an environment name are `const` tables, not
  ladders of comparisons.

## Edge Cases

- A control character is refused in an environment name as well as the listed ones.

## Depth

DEPTH 0.2 (SHALLOW). Exercised by the cascade, values, and discovery suites.

## Grill Log

- **Q:** Why `PUDU_ENV` before `PROFILE`?
  **A:** `PUDU_ENV` names this package's intent; `PROFILE` lets one variable select both the
  configuration profile and the environment files. _Rejected:_ a single variable (forces a choice
  between the two conventions).

## Referenced by

[[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Constants/_MOC]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Domain/Cascade]] · [[src/PuduLangEnvironment/Domain/Keys]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[src/PuduLangEnvironment/Domain/Values]] · [[src/PuduLangEnvironment/Options]]
