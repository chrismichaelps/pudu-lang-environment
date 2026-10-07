---
type: architecture
tags: [architecture, test]
aliases: [Testing]
---

# Testing

Every suite is a file under `test/`, mirroring the module it covers; `pudu test test` runs them all
and each suite names its failed checks on stderr.

| Level | Suites | What they prove |
| --- | --- | --- |
| Domain | `test/PuduLangEnvironment/Domain/**`, `test/PuduLangEnvironment/Utils/**` | every rule of the pure modules: comments, keys, quotes, multi-line values, escapes, inline comments, expansion and its defaults, layering, cascade names and unsafe names, decoding, and value conversion |
| Module | one suite per public module | options and their conflicts, sources of every kind and encoding, probing and cascading on real temporary trees, strict and lenient loading, typed reads, secrets, required keys, child-process hand-off, and redaction |
| Package | `test/Package/LayoutTest` | every shipped module is the root `PuduLangEnvironment` or under it, is named after its path, and agrees with the manifest |
| Vault | `test/Package/VaultTest` | the vault mirrors `src/` page for page, every page has a Grill Log, every exported function is in its page's signatures, every link resolves, and every page lists the pages linking to it |
| Integration | `test/Integration/LoadScenarioTest` | a layered project tree loaded end to end with every option that changes the answer |
| Examples | `examples/*.pudu`, run by CI | the documented programs compile and answer 0 |
| Mutation | [[tools/Mutate]] | the suites notice single-point changes to the pure layer |

The mutation gate runs on pull requests over `Domain/` with a threshold of 100: every valid mutant
is killed. A mutant that cannot change behaviour is removed by simplifying the code rather than
excused.

Every suite that reads a secret-shaped value also checks that no `Problem`, description, or
rendering of the result contains it.

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[handoffs/2026-10-07-initial-package]]
