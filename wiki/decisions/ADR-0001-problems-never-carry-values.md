---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0001 — Problems never carry values

## Context

Environment files hold database passwords, signing keys, and tokens. Refusals end up in logs,
crash reports, and terminal scrollback, which are kept far longer and shared far wider than the
file itself. A refusal that quotes the line it could not read discloses the secret on it.

## Decision

Every refusal is a `Problem` value naming only keys, line numbers, paths, encodings, and option
names. A conversion refusal says which key did not hold which kind of value, not what it held. The
loaded values sit behind a function in [[src/PuduLangEnvironment/Variables]], so `show` over the
result renders `<fn>` in their place, and `Variables.describe` lists names only.

## Consequences

- A refusal can be logged as it is.
- A caller that needs the offending text reads it on purpose with `Variables.find`.

## Rejected

- Quoting the offending line in parse refusals: the most useful text for a reader and the most
  dangerous one for a log.
- A record field holding the merged map: `show` renders every value it holds.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0004-settings-through-the-validator]] · [[decisions/_MOC]] · [[grammar/pudu]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment]] · [[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Variables]]
