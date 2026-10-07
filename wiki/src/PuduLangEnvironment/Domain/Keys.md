---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Keys.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Keys]
---

# PuduLangEnvironment.Domain.Keys

## Purpose

Field names and configuration keys to environment variable names.

## Interface

### Signatures

```pudu
export fn fromField(name: Str) -> Str

export fn fromSetting(key: Str) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Names]].
- **Consumed by:** [[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Configuration]].

## Algorithm

1. `fromField`: each character is upper-cased; an upper-case letter after a lower-case letter or a
   digit is preceded by `_`.
2. `fromSetting`: each `.` and `-` becomes `_` and every other character is upper-cased, the
   spelling the standard configuration reads from the environment.

## Negative Logic (Prohibited Paths)

- A run of capitals is never split, so `apiURL` is `API_URL`, not `API_U_R_L`.

## Edge Cases

- A name already in upper snake case is unchanged.

## Depth

DEPTH 0.3 (SHALLOW). Tested by `test/PuduLangEnvironment/Domain/KeysTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why derive the variable name from the field at all?
  **A:** Most settings are named the same in both places; `@env` is for the rest. _Rejected:_
  requiring `@env` on every field.

## Referenced by

[[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Configuration]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Domain/_MOC]]
