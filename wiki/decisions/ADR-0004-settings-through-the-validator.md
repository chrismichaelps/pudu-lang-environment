---
type: adr
status: Accepted
tags: [adr]
---

# ADR-0004 — Typed settings through the validator

## Context

A program rarely wants loose variables; it wants one record of settings — a port in range, a URL
that parses, a key of the right length — checked once at start-up so a bad deployment stops before
it serves anything. Writing those checks by hand for every program repeats work the Pudu ecosystem
already does in `@chrismichaelps/pudu-lang-validator`.

## Decision

The package depends on `@chrismichaelps/pudu-lang-validator`. [[src/PuduLangEnvironment/Settings]]
builds a caller's record from [[src/PuduLangEnvironment/Variables]] and runs a
`Validator.Validator` over it. Failures of `Error` severity refuse the settings as
`Invalid`, listing each failed property path and the validator's code. The validator's messages
are not carried, because a message template may quote `{PropertyValue}`
([[decisions/ADR-0001-problems-never-carry-values]]).

## Consequences

- One rule language for request validation and configuration validation.
- A caller that wants the full messages runs the validator itself on the record it built.
- Warnings and notes never stop a program from starting.

## Rejected

- A bespoke schema language in this package: a second rule language to learn and maintain.
- Carrying the validator's messages in `Invalid`: one custom message with `{PropertyValue}` would
  print a secret.

## Referenced by

[[CHANGELOG]] · [[decisions/ADR-0005-binding-by-derivation]] · [[decisions/_MOC]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment/Settings]]
