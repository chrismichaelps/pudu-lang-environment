---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0005 — Settings are bound by derivation

## Context

A program declares its settings as a record. Reading each field from a variable by hand repeats
the field list in code that drifts from the type, and Pudu's derive strategies read a record's
fields, types, and attributes at compile time.

## Decision

[[src/PuduLangEnvironment/Binding]] declares `Bind` and `Redacted` with derive strategies for
records. A field's variable is named by `@env("NAME")` or by its name in upper snake case,
`@default("text")` fills an unset one, and `@secret` hides it from `redacted()`. Each field type
reads its text through the `Variable` trait, implemented for text, numbers, truth values, secrets,
and options of those. [[src/PuduLangEnvironment/Settings]] runs the derived `bind` and the
validator together ([[decisions/ADR-0004-settings-through-the-validator]]).

## Consequences

- Adding a setting is adding a field.
- The fluent `Configuring` chain stays hand-written: its methods carry meaning (`withProbe()` picks
  a depth, `withEnvironment(name)` sets two fields, `withFiles(&[])` restores the default) that a
  per-field derive cannot express.

## Rejected

- A caller-supplied build function: the mapping repeated by hand.
- A key-to-field table: names checked only at run time.

## Referenced by

[[CHANGELOG]] · [[decisions/_MOC]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment/Settings]]
