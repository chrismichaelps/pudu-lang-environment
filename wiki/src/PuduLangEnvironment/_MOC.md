---
type: moc
tags: [moc]
---

# PuduLangEnvironment

- [[src/PuduLangEnvironment/Options]] — What a load reads and how: a record with defaults, its validation, and the fluent chain that changes it.
- [[src/PuduLangEnvironment/Source]] — Where one environment file comes from, and its lines.
- [[src/PuduLangEnvironment/Discovery]] — Which files a load reads: given, probed for upward, or cascaded by environment name.
- [[src/PuduLangEnvironment/Loader]] — Reads, parses, and merges the sources the options name, into a map or a settled view.
- [[src/PuduLangEnvironment/Variables]] — The loaded values settled over the process environment, read by type, kept secret, required, and handed to child processes.
- [[src/PuduLangEnvironment/Binding]] — Records read from variables by derivation, and rendered with their secrets hidden.
- [[src/PuduLangEnvironment/Configuration]] — Loaded variables layered over the standard library's application configuration.
- [[src/PuduLangEnvironment/Settings]] — Typed settings bound from variables and accepted by a validator.
- [[src/PuduLangEnvironment/Constants/_MOC]] — names and sentences.
- [[src/PuduLangEnvironment/Domain/_MOC]] — the pure rules.
- [[src/PuduLangEnvironment/Utils/_MOC]] — helpers with no knowledge of environment files.

## Referenced by

[[src/_MOC]]
