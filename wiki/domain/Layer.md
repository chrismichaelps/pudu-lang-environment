---
type: domain
tags: [domain]
---

# Layer

Each source read is one layer of entries, in the order the sources were given or discovered. Layers
merge into one map: a key's first value is kept, and a later value replaces it only when
overwriting is on. The same rule decides which assignment later expansions see. See
[[src/PuduLangEnvironment/Domain/Layers]].

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[subsystems/Loading]]
