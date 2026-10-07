---
type: domain
tags: [domain]
---

# Expansion

With expansion on, `$NAME` and `${NAME}` in an unquoted or double-quoted value are replaced by the
value known for `NAME`: an earlier assignment first, then the process environment, then nothing.
Without overwriting, a non-empty process variable counts as an earlier assignment.
`${NAME:-fallback}` answers the fallback when `NAME` is unset or empty, `${NAME-fallback}` only when
it is unset, and the fallback is itself expanded. A `$` after an odd run of backslashes is literal
and the run is halved; a `$` not followed by a name or a closed brace is literal. See
[[src/PuduLangEnvironment/Domain/Expander]] and [[decisions/ADR-0003-expansion-sees-earlier-assignments]].

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[subsystems/Parsing]]
