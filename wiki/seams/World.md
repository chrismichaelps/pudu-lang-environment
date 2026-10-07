---
type: seam
capacity: BACKBONE
tags: [seam, backbone]
---

# World (seam)

## Classification

Effect boundary between the pure layer and the machine. Files are read by
[[src/PuduLangEnvironment/Source]], existence and real paths are asked by
[[src/PuduLangEnvironment/Discovery]], and the process environment is read once per load by
[[src/PuduLangEnvironment/Loader]] and once per view by [[src/PuduLangEnvironment/Variables]].

## Adapters

- **Machine** — `Loader.read` and `Loader.load` take the process environment as it is.
- **Given** — `Loader.readOver` and `Loader.loadOver` take an environment the caller supplies, so a
  suite or a tool decides what the process holds; `Source.Text` and `Source.Data` replace files.

## Health

No module under `Domain/` reads a file, a directory, or the environment. The process environment is
read into a map once per load rather than scanned per lookup.

## Referenced by

[[architecture/LANGUAGE]] · [[architecture/_MOC]] · [[grammar/pudu]] · [[seams/_MOC]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Source]]
