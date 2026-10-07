---
type: module
path: "@root/src/PuduLangEnvironment.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.5
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment]
---

# PuduLangEnvironment

## Purpose

The package root and its vocabulary: the `Problem` every refusal is, its description, and which
problems a lenient load may skip.

## Interface

### Signatures

```pudu
export type Problem
  = BlankPath
  | MissingFile(Str)
  | Unreadable(Str, Str)
  | Undecodable(Str, Str)
  | UnclosedQuote(Str, Int, Str)
  | NotFound(Array[Str])
  | UnsafeEnvironmentName(Str)
  | InvalidOptions(Array[Str])
  | Missing(Array[Str])
  | Malformed(Str, Str)
  | Invalid(Array[Str])

export fn describe(problem: &Problem) -> Str

export fn isSkippable(problem: &Problem) -> Bool
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Messages]], [[src/PuduLangEnvironment/Utils/Template]].
- **Consumed by:** every public module, package users, and the suites.

## Algorithm

1. `describe` fills the problem's sentence from [[src/PuduLangEnvironment/Constants/Messages]].
2. `isSkippable` holds for a blank path, a missing or unreadable file, undecodable bytes, and
   nothing found; a lenient load records these and continues.

## Negative Logic (Prohibited Paths)

- No variant carries a value read from a file ([[decisions/ADR-0001-problems-never-carry-values]]).
- An unclosed quote, an unsafe environment name, and invalid options are never skippable: each
  means the program would run on a configuration other than the one written.

## Edge Cases

- `Missing` lists every absent key at once; one absent key is a list of one.
- `Malformed` names the key and the kind it should hold, never the text it holds.
- `Invalid` names each property a settings validator refused and its code, never its message.

## Depth

DEPTH 0.5 (MEDIUM). Tested by `test/PuduLangEnvironmentTest.pudu`.

## Grill Log

- **Q:** Why is an unclosed quote fatal even in lenient mode?
  **A:** Reading on would treat the rest of the file as one value or drop it, so the program would
  start with values nobody wrote. _Rejected:_ skipping the file (hides a broken secret file).
- **Q:** Why does `Unreadable` carry the system's reason?
  **A:** It names the path and the operating system's complaint, neither of which is file content.
  _Rejected:_ a bare path (leaves permissions problems unexplained).

## Referenced by

[[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Constants/Messages]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Source]] · [[src/PuduLangEnvironment/Utils/Template]] · [[src/PuduLangEnvironment/Variables]] · [[src/_MOC]]
