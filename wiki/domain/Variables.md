---
type: domain
tags: [domain]
---

# Variables

What `load` answers: the process environment with the loaded values settled over it. A loaded
value replaces a process variable when overwriting is on, or when the process variable is unset or
empty. The view also records which keys the load applied, which sources it read, and which
problems it skipped in lenient mode.

A key is present when its value is not empty. Typed reads answer `Missing` or `Malformed` problems
naming the key; `secret` wraps a value as a `Std.App.Secret`; `require` reports every absent key at
once; `apply` gives a child process the applied keys. See [[src/PuduLangEnvironment/Variables]] and
[[decisions/ADR-0002-a-view-not-a-mutation]].

## Referenced by

[[architecture/LANGUAGE]] · [[domain/_MOC]] · [[grammar/pudu]] · [[subsystems/Loading]]
