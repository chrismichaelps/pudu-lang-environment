---
type: module
path: "@root/src/PuduLangEnvironment/Loader.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.7
depth_status: DEEP
tags: [module]
aliases: [PuduLangEnvironment.Loader]
---

# PuduLangEnvironment.Loader

## Purpose

Reads, parses, and merges the sources the options name, into a map or a settled view.

## Interface

### Signatures

```pudu
export fn read(options: &Options.Options) -> Result[Map[Str, Str], Environment.Problem]

export fn load(options: &Options.Options) -> Result[Variables.Variables, Environment.Problem]

export fn readOver(options: &Options.Options, process: &Map[Str, Str]) -> Result[Map[Str, Str], Environment.Problem]

export fn loadOver(options: &Options.Options, process: &Map[Str, Str]) -> Result[Variables.Variables, Environment.Problem]

export trait Loading {
  fn read(self: &Self) -> Result[Map[Str, Str], Environment.Problem]
  fn load(self: &Self) -> Result[Variables.Variables, Environment.Problem]
}

impl Loading for Options.Options
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Discovery]], [[src/PuduLangEnvironment/Domain/Layers]], [[src/PuduLangEnvironment/Domain/Parser]], [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Source]], [[src/PuduLangEnvironment/Variables]].
- **Consumed by:** package users, the suites, and `examples/`.

## Algorithm

1. Refuse invalid options with `InvalidOptions`, listing every problem.
2. Take the process environment once: `Env.variables()` into a map (`read`, `load`), or the map
   given (`readOver`, `loadOver`).
3. The sources are the given sources, or one `File` per discovered path.
4. Read each source; a skippable problem refuses a strict load and is recorded by a lenient one.
5. Parse each source's lines with the rules the options set, threading the known assignments from
   one source to the next; an unclosed quote is `UnclosedQuote` with the source's label. Without
   overwriting, the known assignments start as the non-empty process variables, which the load
   will keep.
6. Merge the layers ([[src/PuduLangEnvironment/Domain/Layers]]). `read` answers the map; `load`
   settles it over the process environment ([[src/PuduLangEnvironment/Variables]]).

## Negative Logic (Prohibited Paths)

- No load writes the process environment ([[decisions/ADR-0002-a-view-not-a-mutation]]).
- No lookup during parsing scans the process environment; it reads the snapshot.

## Edge Cases

- A lenient load with nothing to read answers an empty map and records why.
- A `File` source given in `sources` is located against `directory` like a given file.

## Depth

DEPTH 0.7 (DEEP). Tested by `test/PuduLangEnvironment/LoaderTest.pudu` and
`test/Integration/LoadScenarioTest.pudu`.

## Grill Log

- **Q:** Why `readOver` and `loadOver`?
  **A:** They are the [[seams/World]] adapter for the process environment: a test or a tool states
  what the process holds instead of depending on the machine it runs on. _Rejected:_ an options
  field for the environment (mixes what to read with what is already there).
- **Q:** Why thread known assignments across sources?
  **A:** A later layer may refer to an earlier one (`.env.local` using a host from `.env`).
  _Rejected:_ expanding each file alone.

- **Q:** Why do known assignments start from the process without overwriting?
  **A:** Without overwriting, a non-empty process variable is what the program sees; an expansion
  that read the file's value instead would build a URL from a port the program never uses.
  _Rejected:_ expansions from the file alone (the view and its own derived values disagree).

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[grammar/pudu]] · [[seams/World]] · [[src/PuduLangEnvironment/Discovery]] · [[src/PuduLangEnvironment/Domain/Layers]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/Source]] · [[src/PuduLangEnvironment/Variables]] · [[src/PuduLangEnvironment/_MOC]] · [[subsystems/Loading]]
