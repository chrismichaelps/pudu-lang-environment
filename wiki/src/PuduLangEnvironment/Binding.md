---
type: module
path: "@root/src/PuduLangEnvironment/Binding.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module]
aliases: [PuduLangEnvironment.Binding]
---

# PuduLangEnvironment.Binding

## Purpose

Records read from variables by derivation, and rendered with their secrets hidden.

## Interface

### Signatures

```pudu
export trait Variable {
  fn fromVariable(key: Str, text: Option[Str]) -> Result[Self, Environment.Problem]
  fn optional() -> Bool
}

impl Variable for Str

impl Variable for Int

impl Variable for Float64

impl Variable for Decimal

impl Variable for Bool

impl Variable for Secret.Secret

impl [A: Variable] Variable for Option[A]

export trait Bind {
  fn bind(variables: &Variables.Variables) -> Result[Self, Environment.Problem]
}

export derive Bind for T: Record

export trait Redacted {
  fn redacted(self: &Self) -> Str
}

export derive Redacted for T: Record
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Constants/Names]], [[src/PuduLangEnvironment/Domain/Keys]], [[src/PuduLangEnvironment/Domain/Values]], [[src/PuduLangEnvironment/Variables]].
- **Consumed by:** [[src/PuduLangEnvironment/Settings]], package users, the suites, and `examples/`.

## Algorithm

1. A field reads the variable `@env("NAME")` names, else its own name in upper snake case
   ([[src/PuduLangEnvironment/Domain/Keys]]).
2. An unset or empty variable takes `@default("text")` when the field has one.
3. First pass: every required field whose variable is unset and has no default is gathered, and
   all of them are refused at once as `Missing`.
4. Second pass: each field converts its text through its `Variable` implementation; the first that
   does not convert is refused as `Malformed` with its key and kind.
5. `Redacted` renders `Name{field: value, ...}` like `show`, with each `@secret` field as
   `[REDACTED]`.

## Negative Logic (Prohibited Paths)

- No conversion refusal holds the text it refused ([[decisions/ADR-0001-problems-never-carry-values]]).
- The gathering pass asks `optional()`, never `fromVariable`: a nested static call through a type
  parameter inside `Meta.collect` fails on the 0.1.3 compiler
  ([pudu-lang#457](https://github.com/chrismichaelps/pudu-lang/issues/457)).

## Edge Cases

- `Option[A]` is `None` when unset and refuses a set value that `A` cannot read.
- A default is converted like any other text, so `@default("eight")` on an `Int` is `Malformed`.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangEnvironment/BindingTest.pudu` and
`test/Integration/LoadScenarioTest.pudu`.

## Grill Log

- **Q:** Why a derive rather than a function the caller writes per record?
  **A:** The record already states every field and type; a derive reads them at compile time, so
  adding a setting is adding a field. _Rejected:_ a hand-written build function per record (the
  mapping repeated in code that drifts from the type).
- **Q:** Why report every missing variable but only the first malformed one?
  **A:** A missing variable is fixed by adding a line, and a deployment usually misses several at
  once; a malformed one needs reading, and the build pass stops at its first failure.
  _Rejected:_ stopping at the first missing variable (one restart per missing key).
- **Q:** Why does `Redacted` render the other fields with `show`?
  **A:** Every type has `show`, so a record needs no extra derive for its plain fields.
  _Rejected:_ requiring `Show.Show` on every field (excludes `Secret.Secret`, which the program
  cannot give an implementation).

## Referenced by

[[CHANGELOG]] · [[architecture/LANGUAGE]] · [[architecture/_MOC]] · [[decisions/ADR-0005-binding-by-derivation]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Domain/Keys]] · [[src/PuduLangEnvironment/Domain/Values]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Variables]] · [[src/PuduLangEnvironment/_MOC]]
