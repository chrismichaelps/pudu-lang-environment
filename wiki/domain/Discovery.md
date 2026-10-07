---
type: domain
tags: [domain]
---

# Discovery

Which files a load reads, in one of three modes:

| Mode | Option | Files |
| --- | --- | --- |
| Given | `files` (default `[".env"]`) | each path, relative to `directory` |
| Probing | `probe: Some(levels)` | the first `.env` found in `directory` or up to `levels` parents |
| Cascade | `cascade: true` | whichever of `.env`, `.env.local`, `.env.<name>`, `.env.<name>.local` exist, in that order |

The cascade's name is `environmentName`, else the first of `PUDU_ENV` and `PROFILE` that is set and
not blank. A name holding `..`, a path separator, or a character a file name cannot hold is refused
outright, so the cascade cannot reach outside its directory. Cascade and probing combine: the
cascade's files are looked for upward. Sources given as text or bytes bypass discovery. See
[[src/PuduLangEnvironment/Discovery]] and [[src/PuduLangEnvironment/Domain/Cascade]].

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[subsystems/Loading]]
