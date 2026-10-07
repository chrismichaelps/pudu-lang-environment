---
type: module
path: "@root/src/PuduLangEnvironment/Options.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Options]
---

# PuduLangEnvironment.Options

## Purpose

What a load reads and how: a record with defaults, its validation, and the fluent chain that
changes it.

## Interface

### Signatures

```pudu
export type Options = {
  strict: Bool,
  files: Array[Str],
  sources: Array[Source.Source],
  encoding: Encoding.Encoding,
  trimValues: Bool,
  overwrite: Bool,
  probe: Option[Int],
  exportSyntax: Bool,
  inlineComments: Bool,
  expansion: Bool,
  cascade: Bool,
  environmentName: Option[Str],
  directory: Str
}

export fn defaults() -> Options

export fn validate(options: &Options) -> Array[Str]

export trait Configuring {
  fn withStrict(self: &Self) -> Self
  fn withoutStrict(self: &Self) -> Self
  fn withFiles(self: &Self, paths: &Array[Str]) -> Self
  fn withSources(self: &Self, given: &Array[Source.Source]) -> Self
  fn withEncoding(self: &Self, encoding: Encoding.Encoding) -> Self
  fn withTrimValues(self: &Self) -> Self
  fn withoutTrimValues(self: &Self) -> Self
  fn withOverwrite(self: &Self) -> Self
  fn withoutOverwrite(self: &Self) -> Self
  fn withProbe(self: &Self) -> Self
  fn withProbeLevels(self: &Self, levels: Int) -> Self
  fn withoutProbe(self: &Self) -> Self
  fn withExportSyntax(self: &Self) -> Self
  fn withoutExportSyntax(self: &Self) -> Self
  fn withInlineComments(self: &Self) -> Self
  fn withoutInlineComments(self: &Self) -> Self
  fn withExpansion(self: &Self) -> Self
  fn withoutExpansion(self: &Self) -> Self
  fn withCascade(self: &Self) -> Self
  fn withEnvironment(self: &Self, name: Str) -> Self
  fn withoutCascade(self: &Self) -> Self
  fn inDirectory(self: &Self, directory: Str) -> Self
}

impl Configuring for Options
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Messages]], [[src/PuduLangEnvironment/Constants/Names]], [[src/PuduLangEnvironment/Domain/Encoding]], [[src/PuduLangEnvironment/Source]].
- **Consumed by:** [[src/PuduLangEnvironment/Discovery]], [[src/PuduLangEnvironment/Loader]], package users.

## Algorithm

1. `defaults()`: lenient, `[".env"]`, no sources, UTF-8, untrimmed, overwriting, not probing,
   no export syntax, inline comments on, no expansion, no cascade, no name, directory `.`.
2. `validate` lists every conflict at once: given files with probing or the cascade, sources with
   files, probing, or the cascade, negative probe levels, and an empty file list.
3. Every `Configuring` method answers a copy with one field changed; `withFiles(&[])` restores
   `[".env"]`, `withProbe()` probes the default four levels, and `withEnvironment(name)` turns the
   cascade on with that name.

## Negative Logic (Prohibited Paths)

- The fluent chain never refuses; conflicts surface from `validate` when the load starts, all at
  once, so a chain reads in any order.

## Edge Cases

- Files are "given" when they differ from `[".env"]`; the default list combines with probing and
  the cascade.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangEnvironment/OptionsTest.pudu`.

## Grill Log

- **Q:** Why a record and a fluent chain both?
  **A:** The record is the configuration and composes by update; the chain reads as a sentence and
  ends in `.load()`. Both produce the same value. _Rejected:_ a builder type distinct from the
  options (two shapes of one thing).
- **Q:** Why refuse negative probe levels instead of using the default?
  **A:** A negative count is a mistake in the caller's arithmetic; replacing it hides the mistake.
  _Rejected:_ silently probing four levels.
- **Q:** Why refuse sources combined with probing?
  **A:** Probing finds files, and given sources replace files; accepting both would ignore one.
  _Rejected:_ letting sources win silently.

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[src/PuduLangEnvironment/Constants/Messages]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Domain/Encoding]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Source]] · [[src/PuduLangEnvironment/_MOC]] · [[subsystems/Loading]]
