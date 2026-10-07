---
type: moc
tags: [moc, architecture]
aliases: [Architecture]
---

# Architecture

## Shape

A caller describes what to read with [[src/PuduLangEnvironment/Options|options]] — a record with
defaults, or the same record changed through a fluent chain — and asks the
[[src/PuduLangEnvironment/Loader|loader]] to `read` or `load` it. The loader validates the options
as a whole, takes one snapshot of the process environment, asks
[[src/PuduLangEnvironment/Discovery|discovery]] which files to read, reads each through
[[src/PuduLangEnvironment/Source|source]], parses it with
[[src/PuduLangEnvironment/Domain/Parser|the parser]], and merges the layers.

`read` answers the merged map. `load` answers [[src/PuduLangEnvironment/Variables|Variables]]: the
merged values settled over the process environment the way an export would settle them, held
behind a function so no rendering shows them, with typed reads, secrets, required keys, and a
hand-off to child processes.

A program's settings record `derives Binding.Bind`, so it reads its own fields from the view
([[src/PuduLangEnvironment/Binding]]), and [[src/PuduLangEnvironment/Settings]] runs a
`pudu-lang-validator` validator over it before the program starts serving. A program built on
`Std.App.Config` layers the view over its declared settings instead
([[src/PuduLangEnvironment/Configuration]]).

## Layers

| Layer | Holds | May import |
| --- | --- | --- |
| `Constants/` | file names, variable names, words, and message templates | nothing |
| `Utils/` | message templates | std, Constants |
| `Domain/` | pure parsing, expansion, layering, cascade names, decoding, key names, and value conversion | Utils, Constants, std |
| public modules | the root vocabulary, options, sources, discovery, the loader, variables, binding, settings, and configuration | Domain, Utils, Constants, each other, std |

`Domain/` performs no effects and imports no public module. Effects live at the [[seams/World]].

## Pages

- [[architecture/LANGUAGE]] — the vocabulary every page uses.
- [[architecture/TESTING]] — the test levels and what each proves.
- [[grammar/pudu]] — the language rules the code follows.

## Referenced by

[[00-INDEX]]
