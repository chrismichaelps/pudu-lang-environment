---
type: handoff
from_role: Implementer
to_role: Forensic Guardian
status: in-progress
tags: [handoff, delivery]
---

# Initial package

## Done

- Issue #1 is the ready issue on `chrismichaelps/pudu-lang-environment`; `feature/1-initial-environment-package` is branched from `dev`,
  which is branched from the `main` baseline.
- Every module under `src/` has its mirrored page with a resolved Grill Log ([[src/_MOC]]).
- Against the published 0.1.3 compiler: `pudu check`, `pudu fmt --check`, and `pudu lint` are clean
  over `src`, `test`, `tools`, and `examples`; every suite passes; every example answers 0; the
  mutation pass over `Domain/` kills every valid mutant ([[architecture/TESTING]]).
- The vault matches the code, and `test/Package/VaultTest` keeps it so.
- A compiler fault met on the way is reported as
  [pudu-lang#457](https://github.com/chrismichaelps/pudu-lang/issues/457) ([[grammar/pudu]]).

## Decided (do not re-litigate)

- Problems never carry values ([[decisions/ADR-0001-problems-never-carry-values]]); loading answers
  a view ([[decisions/ADR-0002-a-view-not-a-mutation]]); expansion reads earlier assignments once
  ([[decisions/ADR-0003-expansion-sees-earlier-assignments]]); settings go through the validator
  ([[decisions/ADR-0004-settings-through-the-validator]]) and are bound by derivation
  ([[decisions/ADR-0005-binding-by-derivation]]).
- `main` receives the package only through a pull request from `dev` with green `checks` and
  `mutation` jobs.

## Open / Remaining

- The repository `chrismichaelps/pudu-lang-environment` exists with its wiki enabled; issue #1 is
  open and pull request #2 carries the package into `dev`.
- A pre-release audit against the full feature set added the configuration layer
  ([[src/PuduLangEnvironment/Configuration]]) and ASCII and UTF-32 decoding, and fixed a secret
  rendered in full by `Redacted` when its field lacked `@secret` ([[src/PuduLangEnvironment/Binding]]).
- Merging into `dev`, then `main`, the `v0.1.0` release, and the API documentation wiki remain.

## Exact next action

Merge pull request #2 into `dev` once its `checks` and `mutation` jobs pass.

## Links

[[00-INDEX]] · [[architecture/TESTING]] · [[CHANGELOG]]

## Referenced by

[[CHANGELOG]] · [[handoffs/_MOC]]
