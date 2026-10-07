---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Values.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.3
depth_status: SHALLOW
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Values]
---

# PuduLangEnvironment.Domain.Values

## Purpose

Text to whole numbers, floats, decimals, and truth values.

## Interface

### Signatures

```pudu
export fn toInt(text: Str) -> Option[Int]

export fn toFloat(text: Str) -> Option[Float64]

export fn toDecimal(text: Str) -> Option[Decimal]

export fn toBool(text: Str) -> Option[Bool]
```

### Linkage

- **Requires:** [[src/PuduLangEnvironment/Constants/Names]].
- **Consumed by:** [[src/PuduLangEnvironment/Binding]], [[src/PuduLangEnvironment/Variables]].

## Algorithm

1. Surrounding space is ignored by every conversion.
2. A truth value is `true` or `false` in any letter case.

## Negative Logic (Prohibited Paths)

- `yes`, `1`, and `on` are not truth values; each would be a guess about the writer's intent.

## Edge Cases

- A whole number admits a sign and refuses one too large for `Int`.

## Depth

DEPTH 0.3 (SHALLOW). Tested by `test/PuduLangEnvironment/Domain/ValuesTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why only `true` and `false`?
  **A:** A flag that reads `1` as true and `on` as false by accident is a security switch left open;
  one spelling per value cannot be misread. _Rejected:_ a list of truthy words.

## Referenced by

[[src/PuduLangEnvironment/Binding]] · [[src/PuduLangEnvironment/Constants/Names]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[src/PuduLangEnvironment/Variables]]
