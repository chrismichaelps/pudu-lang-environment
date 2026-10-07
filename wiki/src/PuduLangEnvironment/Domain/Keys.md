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

Field names to environment variable names.

## Interface

### Signatures

```pudu
export fn fromField(name: Str) -> Str
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangEnvironment/Binding]].

## Algorithm

1. Each character is upper-cased; an upper-case letter after a lower-case letter or a digit is
   preceded by `_`.

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

[[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Domain/_MOC]]
