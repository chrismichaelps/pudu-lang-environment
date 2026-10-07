---
type: module
path: "@root/src/PuduLangEnvironment/Source.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Source]
---

# PuduLangEnvironment.Source

## Purpose

Where one environment file comes from, and its lines.

## Interface

### Signatures

```pudu
export type Source
  = File(Str)
  | Text(Str, Str)
  | Data(Str, Bytes)

export fn label(source: &Source) -> Str

export fn lines(source: &Source, encoding: Encoding.Encoding) -> Result[Array[Str], Environment.Problem]
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Domain/Encoding]], [[src/PuduLangEnvironment]].
- **Consumed by:** [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment/Loader]], package users.

## Algorithm

1. `File(path)`: a blank path is `BlankPath`; a path that does not exist is `MissingFile`; bytes
   that cannot be read are `Unreadable`; bytes that do not decode are `Undecodable`.
2. `Data(label, bytes)` decodes the bytes; `Text(label, text)` is already text.
3. The text is split into lines on `\n` or `\r\n`.

## Negative Logic (Prohibited Paths)

- Decoding happens here, at the [[seams/World]]; the rules live in
  [[src/PuduLangEnvironment/Domain/Encoding]].

## Edge Cases

- A leading byte-order mark is dropped whatever the declared encoding.
- An empty file has no lines and contributes no entries.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangEnvironment/SourceTest.pudu`.

## Grill Log

- **Q:** Why read bytes rather than text for files?
  **A:** A declared encoding other than UTF-8 needs the bytes, and one read path keeps the problems
  the same for every encoding. _Rejected:_ `Io.read` for UTF-8 files only.

## Referenced by

[[architecture/_MOC]] · [[grammar/pudu]] · [[seams/World]] · [[src/PuduLangEnvironment/Domain/Encoding]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/_MOC]] · [[subsystems/Loading]]
