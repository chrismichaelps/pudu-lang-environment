---
type: module
path: "@root/tools/Mutate.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
tags: [module, tool]
---

# Mutate (tool)

## Purpose

Mutation testing: apply one operator change at a time to source files, run the checks and the suites, and report survivors.

## Interface

`pudu run tools/Mutate.pudu [--file <path> | --domain] [--suites <path>] [--every <n>] [--threshold <percent>] [--dry-run]`,
with `PUDU_BIN` naming the compiler (default `pudu`) and `--suites` the suites to run (default `test`).

## Algorithm

1. Mutants are operator swaps at code positions outside strings, character literals, comments, and imports; comparisons must be spaced so type brackets are not mutated.
2. A mutant that fails `pudu check` is invalid; one whose suites still pass survived.
3. `--domain` limits the run to the pure layer; `--every n` samples; `--threshold` fails the run below a score.

## Negative Logic (Prohibited Paths)

- Every mutated file is restored before the next mutant, whatever the verdict.
- A mutant whose suites run past 30 seconds counts as killed.
- The harness runs on a committed tree only: a run stopped mid-mutant leaves that one change,
  which `git diff` shows and `git checkout` removes.

## Grill Log

- **Q:** Why mutate only the domain in pull requests?
  **A:** It holds every rule about what a file means, where a single-point change is most likely to go unnoticed, and it runs in minutes. _Rejected:_ the full tree on every pull request.
- **Q:** Why run the domain mutants against the domain suites only?
  **A:** The pure layer must be pinned by its own suites; the suites that build temporary trees take longer and would only hide a gap in the domain suites. _Rejected:_ the whole tree's suites per mutant.

## Referenced by

[[architecture/TESTING]] · [[src/_MOC]]
