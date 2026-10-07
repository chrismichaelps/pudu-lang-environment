---
type: module
path: "@root/src/PuduLangEnvironment/Constants/Messages.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.2
depth_status: SHALLOW
tags: [module]
aliases: [PuduLangEnvironment.Constants.Messages]
---

# PuduLangEnvironment.Constants.Messages

## Purpose

Every sentence the package reports: one per problem, one per invalid option, and the description
of a view.

## Interface

### Signatures

```pudu
export const BLANK_PATH: Str

export const MISSING_FILE: Str

export const UNREADABLE: Str

export const UNDECODABLE: Str

export const UNCLOSED_QUOTE: Str

export const NOT_FOUND: Str

export const UNSAFE_ENVIRONMENT_NAME: Str

export const INVALID_OPTIONS: Str

export const MISSING: Str

export const MALFORMED: Str

export const INVALID_SETTINGS: Str

export const FAILED_PROPERTY: Str

export const FILES_WITH_PROBE: Str

export const FILES_WITH_CASCADE: Str

export const SOURCES_WITH_FILES: Str

export const SOURCES_WITH_PROBE: Str

export const SOURCES_WITH_CASCADE: Str

export const NEGATIVE_PROBE: Str

export const NO_FILES: Str

export const DESCRIBED: Str

export const NO_SOURCES: Str

export const LIST_SEPARATOR: Str

export const PROBLEM_SEPARATOR: Str
```

### Linkage

- **Requires:** nothing.
- **Consumed by:** [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment/Variables]].

## Algorithm

1. Templates number their slots `<1>`, `<2>`, filled by [[src/PuduLangEnvironment/Utils/Template]].

## Negative Logic (Prohibited Paths)

- No template has a slot for a value read from a file.

## Edge Cases

- Lists inside a sentence are joined with `LIST_SEPARATOR`.

## Depth

DEPTH 0.2 (SHALLOW). Exercised by every suite that checks a description.

## Grill Log

- **Q:** Why keep every sentence in one module?
  **A:** Wording is reviewed in one place, and the review that matters — does any sentence quote a
  value — is one read. _Rejected:_ sentences beside each caller.

## Referenced by

[[src/PuduLangEnvironment]] · [[src/PuduLangEnvironment/Constants/_MOC]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Variables]]
