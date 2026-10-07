---
type: module
path: "@root/src/PuduLangEnvironment/Discovery.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Discovery]
---

# PuduLangEnvironment.Discovery

## Purpose

Which files a load reads: given, probed for upward, or cascaded by environment name.

## Interface

### Signatures

```pudu
export type Found = { paths: Array[Str], skipped: Array[Environment.Problem] }

export fn discover(options: &Options.Options, process: &Map[Str, Str]) -> Result[Found, Environment.Problem]

export fn probe(start: Str, levels: Int, names: &Array[Str]) -> Option[Str]

export fn locate(directory: Str, path: Str) -> Str
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Names]], [[src/PuduLangEnvironment/Domain/Cascade]], [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment]].
- **Consumed by:** [[src/PuduLangEnvironment/Loader]].

## Algorithm

1. Cascade: choose the name ([[src/PuduLangEnvironment/Domain/Cascade]]); an unsafe name is
   refused. The directory is the probed one when probing, else the directory of the first file.
   Every candidate that exists is read, in candidate order.
2. Probing without the cascade: the first directory, from `directory` upward through `levels`
   parents, that holds `.env`.
3. Otherwise each given file, located against `directory`.
4. When nothing is found, a strict load is refused with `NotFound` listing every path looked for;
   a lenient one records the problem and reads nothing.

## Negative Logic (Prohibited Paths)

- No candidate name is built from an unchecked environment name.
- Probing never climbs more than `levels` parents and stops at the root.

## Edge Cases

- Given files are not checked here; a missing one is the source's problem, recorded per file.
- `directory` is resolved to its real path before probing, so `..` and links climb correctly.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangEnvironment/DiscoveryTest.pudu` on temporary trees.

## Grill Log

- **Q:** Why probe from the working directory?
  **A:** A Pudu program runs from its project, and its environment files sit there or above;
  `directory` moves the start for anything else. _Rejected:_ the directory of the running program
  (a build or install path, not the project).
- **Q:** Why answer existing cascade files only, but refuse when none exists in strict mode?
  **A:** Every layer is optional on its own; a cascade with no layer at all means the program looked
  in the wrong place. _Rejected:_ requiring `.env` itself.

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[domain/Discovery]] · [[grammar/pudu]] · [[seams/World]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Domain/Cascade]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/_MOC]] · [[subsystems/Loading]]
