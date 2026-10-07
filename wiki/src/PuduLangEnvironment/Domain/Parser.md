---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Parser.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.8
depth_status: DEEP
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Parser]
---

# PuduLangEnvironment.Domain.Parser

## Purpose

Lines of an environment file to entries, following the rules the options set.

## Interface

### Signatures

```pudu
export type Rules = { trimValues: Bool, exportSyntax: Bool, inlineComments: Bool, expansion: Bool, overwrite: Bool }

export type Unclosed = { line: Int, key: Str }

export type Parsed = { entries: Array[Layers.Entry], known: Map[Str, Str] }

export fn parse(rows: &Array[Str], rules: &Rules, known: Map[Str, Str], process: &Map[Str, Str]) -> Result[Parsed, Unclosed]

export fn lines(text: Str) -> Array[Str]
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Names]], [[src/PuduLangEnvironment/Domain/Expander]], [[src/PuduLangEnvironment/Domain/Layers]].
- **Consumed by:** [[src/PuduLangEnvironment/Loader]].

## Algorithm

1. Skip blank lines, lines whose first visible character is `#`, lines without `=` after the first
   character, and empty keys ([[domain/EnvironmentFile]]).
2. The key is the trimmed text before `=`; with export syntax, a leading `export` followed by space
   is dropped.
3. A value starting (after space) with `'` or `"` runs to the closing quote: the next quote of that
   kind not escaped by the backslash before it, itself unescaped, joining further lines with `\n` until one is found. No
   closing quote by the last line answers `Unclosed` with the opening line and the key.
4. A quoted body has `\<quote>` read as the quote; a double-quoted one is then expanded; finally
   `\\` is read as one backslash.
5. An unquoted value drops an inline comment when enabled, then is expanded when enabled.
6. Trimming, when on, applies to every value last.
7. Each entry is recorded, and `known` keeps it by the overwrite rule
   ([[src/PuduLangEnvironment/Domain/Layers]]) for the expansions after it.

## Negative Logic (Prohibited Paths)

- A single-quoted value is never expanded.
- `Unclosed` names a key and a line, never the text.

## Edge Cases

- `export` alone, or as the start of a longer key (`exporter=1`), is a key, not the prefix.
- A `#` with no space before it (`a#b`) is part of the value.
- Text after a closing quote is ignored.

## Depth

DEPTH 0.8 (DEEP). Tested by `test/PuduLangEnvironment/Domain/ParserTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why does a comment line allow leading space?
  **A:** An indented `# KEY=value` is a commented-out assignment; reading it as the key `# KEY`
  would load it. _Rejected:_ comments only at the first column.
- **Q:** Why strip `export` only as a whole word?
  **A:** A key named `exporter` or `EXPORT_PATH` must survive. _Rejected:_ removing every
  occurrence of the prefix text.
- **Q:** Why are `\n` and `\t` not escapes inside double quotes?
  **A:** A double-quoted value keeps real line breaks across lines, and backslash sequences other
  than an escaped quote and `\\` stay as written, so a Windows path keeps its backslashes.
  _Rejected:_ C-style escapes (rewrite paths like `C:\new`).

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[domain/EnvironmentFile]] · [[grammar/pudu]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Domain/Expander]] · [[src/PuduLangEnvironment/Domain/Layers]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[src/PuduLangEnvironment/Loader]] · [[subsystems/Parsing]]
