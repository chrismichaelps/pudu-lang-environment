---
type: changelog
tags: [changelog]
---

# Changelog

## 2026-10-07 — Initial package (#1)

- The package `@chrismichaelps/pudu-lang-environment` 0.1.0 with the module root
  `PuduLangEnvironment`, the language range `>=0.1.3 <0.2.0`, and one dependency,
  `@chrismichaelps/pudu-lang-validator` `^0.1.1` ([[decisions/ADR-0004-settings-through-the-validator]]).
- The file format: comments, blank lines, keys, quoted and multi-line values, escaped quotes and
  backslashes, inline comments, export syntax, trimming, and CRLF lines
  ([[src/PuduLangEnvironment/Domain/Parser]], [[domain/EnvironmentFile]]).
- Expansion of `$NAME`, `${NAME}`, `${NAME:-default}`, and `${NAME-default}`, once per value from
  earlier assignments and the process environment
  ([[src/PuduLangEnvironment/Domain/Expander]], [[decisions/ADR-0003-expansion-sees-earlier-assignments]]).
- Layers merged by the overwrite rule and settled over the process environment
  ([[src/PuduLangEnvironment/Domain/Layers]]); UTF-8, Latin-1, and UTF-16 sources with byte-order
  marks ([[src/PuduLangEnvironment/Domain/Encoding]]).
- Given files, upward probing, and the environment cascade with safe names
  ([[src/PuduLangEnvironment/Discovery]], [[src/PuduLangEnvironment/Domain/Cascade]]).
- [[src/PuduLangEnvironment/Options]] as a validated record and a fluent chain;
  [[src/PuduLangEnvironment/Loader]] with `read`, `load`, `readOver`, `loadOver`, and a chain that
  ends in `.read()` or `.load()`.
- [[src/PuduLangEnvironment/Variables]]: typed reads, secrets, required keys, child-process
  hand-off, and a description without values; loading never writes the process environment
  ([[decisions/ADR-0002-a-view-not-a-mutation]]).
- Settings records bound by `derives Binding.Bind` with `@env`, `@default`, and `@secret`, rendered
  safely by `derives Binding.Redacted`, and checked by a validator
  ([[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Settings]],
  [[decisions/ADR-0005-binding-by-derivation]]).
- No problem, description, or rendering carries a value read from a file
  ([[decisions/ADR-0001-problems-never-carry-values]]).
- A compiler fault found while writing the binding derive was reported as
  [pudu-lang#457](https://github.com/chrismichaelps/pudu-lang/issues/457) and worked around
  ([[grammar/pudu]]).
- Suites for every module, a package layout test, a vault parity test, an integration scenario,
  runnable examples, and mutation testing of the pure layer ([[architecture/TESTING]],
  [[handoffs/2026-10-07-initial-package]]).

## Referenced by

[[00-INDEX]] · [[handoffs/2026-10-07-initial-package]]
