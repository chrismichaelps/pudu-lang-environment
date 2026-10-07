---
type: grammar
language: Pudu
version: "0.1.3"
tags: [grammar]
aliases: [Grammar — Pudu, Pudu Grammar]
---

# Grammar — Pudu

The Pudu surface this repository is written against, pinned to compiler `0.1.3` as published in
its release archive. Where this page and the compiler disagree, the compiler wins and this page is
corrected in the same change.

## SDK Discovery Map

| Need | Module | Entry points |
| --- | --- | --- |
| Process environment | `Std.Env` | `variables` (every name and value, read once per load) |
| Files | `Std.Io` | `exists`, `readAllBytes` |
| Real paths | `Std.Fs` | `canonical` |
| Path arithmetic | `Std.Path` | `join`, `parentOf`, `directoryOf`, `isAbsolute` |
| Bytes | `Std.Bytes` | `toText`, `toArray`, `drop`, `startsWith`, `fromArray` |
| Characters | `Std.Char` | `isLetter`, `isDigit`, `isWhitespace`, `toLower` |
| Text | `Std.Text` | `wholeOf`, `trimStart`, `trimEnd`, `fromChars` |
| Exact numbers | `Std.Decimal` | `parse` |
| Secrets | `Std.App.Secret` | `secret`, `reveal`, `redact` |
| Application configuration | `Std.App.Config` | `Config`, `Setting`, `declaring`, `sourceOf`, `Layering` |
| Reflection in derives | `Std.Meta` | `build`, `collect`, `nameOf`, `Field.get`, `Field.has`, `Field.attributeOr` |
| Child processes | `Std.Process` | `Launch`, `Launching.withVariable` |
| Collections | `Std.List`, `Std.Map` | `List.sortBy`, `Map.fromPairs`, `Map.get`, `Map.insert`, `Map.keys` |
| Temporary trees | `Std.Fs` | `withTemporaryDirectory` (examples and suites) |
| Tests | `Std.Test` | `suite`, `equals`, `that`, `run`, `failuresOf`, `report` |

`Std.Env` reads the process environment and offers no way to change it. Loading therefore answers
a [[domain/Variables|Variables]] view instead of writing into the process; see
[[decisions/ADR-0002-a-view-not-a-mutation]].

## Imports / Namespaces

- One module per file; the module name is the path under its source root with `/` as `.`:
  `src/PuduLangEnvironment/Domain/Parser.pudu` is `module PuduLangEnvironment.Domain.Parser`.
- Every import is qualified and aliased: `import Std.Io as Io`. Nothing is imported implicitly.
- Suites under `test/` and programs under `examples/` import package modules through the
  manifest's source root.
- A trait is implemented for a type another module declares by the module that declares the
  trait: `Loader` implements `Loading` for `Options.Options`, so the fluent chain ends in `.load()`
  without `Options` importing `Loader`.

## Core Primitives

- Records: `export type Point = { x: Int, y: Int }`, built as `Point{x: 1, y: 2}`, updated as
  `Point{..p, y: 5}`. An imported record is updated with its qualified name:
  `Options.Options{..Options.defaults(), strict: true}`.
- Sum types: `export type Source = File(Str) | Text(Str, Str)`, taken apart with `match`.
- A trait method takes `self: &Self` and answers `Self` for a fluent chain:
  `Options.defaults().withExpansion().withoutOverwrite()`.
- A function stored in a record field is called as `(record.field)(argument)`; `show` renders it as
  `<fn>`, which is why [[src/PuduLangEnvironment/Variables]] keeps its values behind one.
- `Option[T]` and `Result[T, E]` helpers are module functions (`Option.unwrapOr(value, fallback)`).
- `?` propagates `None` or `Err` from a function whose return type is the same family.
- Module scope holds only `const`. Lookup tables are `const` tables built with `setOf([...])` or
  `mapOf([...])`.
- A type opts into generated implementations with a trailing `derives` clause:
  `type Server = { port: Int } derives Binding.Bind, Binding.Redacted`. A strategy is written
  `export derive Trait for T: Record { ... }` and reads fields through `Std.Meta`: `Meta.build`
  constructs the record (a closure answering `Result` stops at the first `Err`), `Meta.collect`
  gathers one answer per field, and `field.has(name)` / `field.attributeOr(name, fallback)` read
  attributes such as `@env("PORT")`, `@default("info")`, and `@secret`.
- A static trait method is called on a type: `F.fromVariable(key, text)`, `T.bind(variables)`.
- Borrowing: `&T` parameters are read-only views; `*view` copies a borrowed value into an owned one.

## Text

- `Str` is UTF-8. `text.chars()` answers an `Array[Char]` indexed in constant time; scanners walk
  that array, never `charAt`, which counts from the start each call.
- `text.lines()` splits on `\n` only and keeps a `\r` that ends a CRLF line;
  [[src/PuduLangEnvironment/Domain/Parser]] splits lines itself.
- `Map.entries()` and `Map.keys()` answer in key order.
- `" 12 ".toInt()` is `None`: `Str.toInt` admits no surrounding space. `Text.wholeOf` trims and
  admits a sign.
- `charFromCode(n)` answers `Option[Char]`; `convertInteger[Int](byte)` answers `Option[Int]`.
- `Str.toFloat` answers `Option[Float64]`; a trait is implemented for `Float64`, not `Float`.

## Architectural Laws

- Dependency direction is inward: the public modules at `PuduLangEnvironment.*` use
  `PuduLangEnvironment.Domain.*`, which uses `Utils` and `Constants`. `Domain` never imports a
  public module and performs no effects.
- Files, the working directory, and the process environment are touched only by
  [[src/PuduLangEnvironment/Source]], [[src/PuduLangEnvironment/Discovery]],
  [[src/PuduLangEnvironment/Loader]], and [[src/PuduLangEnvironment/Variables]]
  (see [[seams/World]]).
- Failures are values: every refusal is a `Problem` variant. Nothing is thrown, and no `Problem`
  carries a value read from a file ([[decisions/ADR-0001-problems-never-carry-values]]).
- Every module the package ships is `PuduLangEnvironment` or lives under
  `src/PuduLangEnvironment/`, so a program that installs the package keeps every other module name.

## Syntax Rules / Naming

- Types, traits, modules, and variants are `PascalCase`; values `camelCase`; constants
  `UPPER_SNAKE_CASE`.
- Every file header and exported type carries the FMCF anchor, one line:
  `/** @Namespace.Entity.Role — intent */`, five to eight words of intent.
- Every `fn`, `export fn`, and `const` carries a `///` doc comment of one or two lines stating
  what it answers or holds, in the voice of the standard library's own documentation. It states
  the contract, not the steps.
- Rationale belongs in the mirrored page's Grill Log. No narration, history, or explanation of the
  obvious in code.

## Prohibited Patterns (verified against the 0.1.3 compiler)

- **A brace inside a string literal is interpolation.** A literal brace is `\{` or `\}`.
- **`Array.get(i)` and `items[i]` stop the program when `i` is out of range.** Guard the index or
  use `List.get` for an `Option`.
- **`scope`, `module`, `where`, and `task` are keywords**; none can name a binding or a field.
- **Matching a borrowed value binds its parts as owned values.**
- **A unit value is matched as `Ok(_)`, not `Ok(())`.**
- **A static call through a type parameter inside `Meta.collect`** (`F.fromVariable` whose
  `Option[A]` implementation calls `A.fromVariable`) fails at run time with E7001 on 0.1.3
  ([pudu-lang#457](https://github.com/chrismichaelps/pudu-lang/issues/457)); `Meta.build` is
  unaffected. Gather in `Meta.collect` with a method that does not recurse.
- **`show` over `Std.App.Secret.Secret`** prints its raw value; render secrets through
  `Binding.Redacted` or `Secret.redact`.
- **`Env.variable` inside a loop** scans the whole environment each call; read `Env.variables()`
  once into a map.
- **A `Problem` or description built from a value** discloses a secret; build it from keys, line
  numbers, and paths only.
- **`show` over a record holding a value from a file** prints it; such records keep values behind a
  function field.

## Senior Definition Needed

(none open)

## Referenced by

[[00-INDEX]] · [[CHANGELOG]] · [[architecture/_MOC]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment]] · [[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Configuration]] · [[src/PuduLangEnvironment/Constants/Messages]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Domain/Cascade]] · [[src/PuduLangEnvironment/Domain/Encoding]] · [[src/PuduLangEnvironment/Domain/Expander]] · [[src/PuduLangEnvironment/Domain/Keys]] · [[src/PuduLangEnvironment/Domain/Layers]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[src/PuduLangEnvironment/Domain/Values]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Source]] · [[src/PuduLangEnvironment/Utils/Template]] · [[src/PuduLangEnvironment/Variables]] · [[tools/Mutate]]
