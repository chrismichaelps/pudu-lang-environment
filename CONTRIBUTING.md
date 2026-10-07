# Contributing to pudu-lang-environment

The wiki vault under `wiki/` is the source of truth. Read `wiki/00-INDEX.md`, the architecture
map, and the grammar page before changing code, and write or update a module's mirrored page under
`wiki/src/` before its code.

## Branches

- `main` holds released versions only. It changes through a pull request from `dev`.
- `dev` is the integration branch.
- Work happens on `feature/<issue>-<slug>`, `fix/<issue>-<slug>`, or `docs/<issue>-<slug>`,
  branched from `dev` and merged back through a pull request.

## Commits

Semantic commits that name the issue: `feat(parser): read escaped quotes refs #12`. Types are
`feat`, `fix`, `perf`, `refactor`, `test`, `docs`, `ci`, and `chore`. Keep each commit to one
change; code, tests, and the matching wiki pages move together.

## Code

- Dependencies point inward: the public modules use `Domain`, which uses `Utils` and
  `Constants`. `Domain` performs no effects and imports no public module.
- Files, the process environment, and the working directory are reached only by `Source`,
  `Discovery`, and `Loader`; every rule about what a file means lives in `Domain`.
- No value read from an environment file appears in a problem, a description, or a rendering.
- Every module the package ships is `PuduLangEnvironment` or lives under `src/PuduLangEnvironment/`.
- Every file and exported type starts with a one-line `/** @Namespace.Entity.Role — intent */`
  anchor, and every function and constant carries a short `///` comment saying what it answers.
- No dead code: every declaration has a caller.
- Keep files under 500 lines.

## Checks

Every change passes all of these:

```bash
pudu check $(find src test tools examples -name '*.pudu')
pudu fmt --check src test tools examples
pudu lint src test tools examples
pudu test test
```

Changes to logic also run mutation testing on the files they touch, and no new mutant may survive
without a written reason:

```bash
pudu run tools/Mutate.pudu --file src/PuduLangEnvironment/Domain/Parser.pudu
```

## Pull requests

Open the pull request against `dev`, describe the behaviour and how it was verified, and link the
issue with `Closes #N`.
