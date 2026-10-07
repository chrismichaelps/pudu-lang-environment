---
type: module
path: "@root/src/PuduLangEnvironment/Variables.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Variables]
---

# PuduLangEnvironment.Variables

## Purpose

The loaded values settled over the process environment, read by type, kept secret, required, and
handed to child processes.

## Interface

### Signatures

```pudu
export type Variables = {
  lookup: fn(Str) -> Option[Str],
  applied: Array[Str],
  sources: Array[Str],
  skipped: Array[Environment.Problem]
}

export fn settled(loaded: &Map[Str, Str], current: &Map[Str, Str], overwrite: Bool, sources: Array[Str], skipped: Array[Environment.Problem]) -> Variables

export fn process() -> Variables

export fn find(variables: &Variables, key: Str) -> Option[Str]

export fn has(variables: &Variables, key: Str) -> Bool

export fn text(variables: &Variables, key: Str) -> Result[Str, Environment.Problem]

export fn int(variables: &Variables, key: Str) -> Result[Int, Environment.Problem]

export fn findInt(variables: &Variables, key: Str) -> Option[Int]

export fn float(variables: &Variables, key: Str) -> Result[Float64, Environment.Problem]

export fn findFloat(variables: &Variables, key: Str) -> Option[Float64]

export fn decimal(variables: &Variables, key: Str) -> Result[Decimal, Environment.Problem]

export fn findDecimal(variables: &Variables, key: Str) -> Option[Decimal]

export fn bool(variables: &Variables, key: Str) -> Result[Bool, Environment.Problem]

export fn findBool(variables: &Variables, key: Str) -> Option[Bool]

export fn secret(variables: &Variables, key: Str) -> Result[Secret.Secret, Environment.Problem]

export fn require(variables: &Variables, keys: &Array[Str]) -> Result[(), Environment.Problem]

export fn apply(variables: &Variables, launch: &Process.Launch) -> Process.Launch

export fn describe(variables: &Variables) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Messages]], [[src/PuduLangEnvironment/Domain/Layers]], [[src/PuduLangEnvironment/Domain/Values]], [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Utils/Template]].
- **Consumed by:** [[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Loader]], [[src/PuduLangEnvironment/Settings]], package users, the suites, and `examples/`.

## Algorithm

1. `settled` settles the loaded map over the process map ([[src/PuduLangEnvironment/Domain/Layers]])
   and closes `lookup` over the result.
2. `find` answers a value that is set and not empty; `has` asks the same.
3. Each typed read answers `Missing([key])` when absent and `Malformed(key, kind)` when the text
   does not convert ([[src/PuduLangEnvironment/Domain/Values]]); each `find…` answers `None` for
   both.
4. `require` answers `Missing` with every absent key, in the order asked.
5. `apply` gives the launch every applied key and its value.
6. `describe` names the count of applied keys and the sources, never a value.

## Negative Logic (Prohibited Paths)

- No field holds a value: `show(variables)` renders the lookup as `<fn>`
  ([[decisions/ADR-0001-problems-never-carry-values]]).
- `apply` gives a child only applied keys; the child inherits the rest as the launch says.

## Edge Cases

- A variable set to the empty string is absent, the way an unset one is.
- A process variable that the load did not override is still readable through the view.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangEnvironment/VariablesTest.pudu`.

## Grill Log

- **Q:** Why is an empty value absent?
  **A:** `KEY=` in a file usually marks a value still to be filled in; a typed read that answered
  `""` or failed to convert it would hide that. _Rejected:_ treating `""` as present.
- **Q:** Why does `apply` add only applied keys?
  **A:** The launch already decides whether the child inherits the process environment; adding
  every process variable again would defeat `isolated()`. _Rejected:_ adding the whole view.

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[decisions/ADR-0001-problems-never-carry-values]] · [[decisions/ADR-0002-a-view-not-a-mutation]] · [[decisions/ADR-0004-settings-through-the-validator]] · [[domain/Variables]] · [[grammar/pudu]] · [[seams/World]] · [[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Configuration]] · [[src/PuduLangEnvironment/Constants/Messages]] · [[src/PuduLangEnvironment/Domain/Layers]] · [[src/PuduLangEnvironment/Domain/Values]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Utils/Template]] · [[src/PuduLangEnvironment/_MOC]] · [[subsystems/Loading]]
