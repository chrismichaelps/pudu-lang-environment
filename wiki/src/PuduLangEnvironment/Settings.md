---
type: module
path: "@root/src/PuduLangEnvironment/Settings.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Settings]
---

# PuduLangEnvironment.Settings

## Purpose

Typed settings bound from variables and accepted by a validator.

## Interface

### Signatures

```pudu
export fn bind[T: Binding.Bind](variables: &Variables.Variables, validator: &Validator.Validator[T]) -> Result[T, Environment.Problem]

export fn check[T](settings: T, validator: &Validator.Validator[T]) -> Result[T, Environment.Problem]
```

### Linkage

- **Requires:** `PuduLangValidator.Result`, `PuduLangValidator.Validator`, [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Constants/Messages]], [[src/PuduLangEnvironment/Utils/Template]], [[src/PuduLangEnvironment/Variables]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. `bind` reads the record through its derived `Bind` ([[src/PuduLangEnvironment/Binding]]); a
   `Missing` or `Malformed` read stops it there.
2. `check` runs the validator; failures of `Error` severity refuse the record as `Invalid` with
   `path (code)` for each, in rule order.

## Negative Logic (Prohibited Paths)

- No validator message reaches a `Problem` ([[decisions/ADR-0004-settings-through-the-validator]]).

## Edge Cases

- A validator whose only failures are warnings or notes accepts the record.
- A validator with no rules accepts every record.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangEnvironment/SettingsTest.pudu`.

## Grill Log

- **Q:** Why does `bind` require a derived `Bind` instead of taking a build function?
  **A:** The record's fields and attributes already say which variable feeds which field
  ([[decisions/ADR-0005-binding-by-derivation]]); `check` remains for records built another way.
  _Rejected:_ a caller-written build function (the mapping repeated by hand).

## Referenced by

[[CHANGELOG]] · [[architecture/LANGUAGE]] · [[architecture/_MOC]] · [[decisions/ADR-0004-settings-through-the-validator]] · [[decisions/ADR-0005-binding-by-derivation]] · [[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Variables]] · [[src/PuduLangEnvironment/_MOC]]
