---
type: module
path: "@root/src/PuduLangEnvironment/Utils/Template.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Utils.Template]
---

# PuduLangEnvironment.Utils.Template

## Purpose

Fills the numbered slots of a message template.

## Interface

### Signatures

```pudu
export fn fill(template: Str, values: &Array[Str]) -> Str
```

### Linkage

- **Requires:** nothing beyond std.
- **Consumed by:** [[src/PuduLangEnvironment]], [[src/PuduLangEnvironment/Variables]].

## Algorithm

1. One pass over the template: `<n>` with `n` naming a given value is replaced by it; anything else
   is copied.

## Negative Logic (Prohibited Paths)

- A filled value is never scanned again, so a value holding `<2>` is not filled twice.

## Edge Cases

- A slot without a value, or `<` not followed by digits and `>`, is kept as written.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangEnvironment/Utils/TemplateTest.pudu`.

## Grill Log

- **Q:** Why numbered slots instead of interpolation?
  **A:** A brace in a Pudu string is interpolation, and templates live as constants filled later.
  _Rejected:_ replacing each slot in turn (fills a slot inside an earlier value).

## Referenced by

[[src/PuduLangEnvironment]] · [[src/PuduLangEnvironment/Constants/Messages]] · [[src/PuduLangEnvironment/Settings]] · [[src/PuduLangEnvironment/Utils/_MOC]] · [[src/PuduLangEnvironment/Variables]]
