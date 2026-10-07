---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Encoding.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Encoding]
---

# PuduLangEnvironment.Domain.Encoding

## Purpose

Bytes to text in a declared encoding, honouring a byte-order mark.

## Interface

### Signatures

```pudu
export type Encoding
  = Utf8
  | Latin1
  | Utf16Le
  | Utf16Be

export fn decode(data: &Bytes, declared: Encoding) -> Option[Str]

export fn name(encoding: &Encoding) -> Str
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment/Source]].

## Algorithm

1. A UTF-8, UTF-16 little-endian, or UTF-16 big-endian byte-order mark selects that encoding and
   is dropped; otherwise the declared encoding applies.
2. UTF-8 is checked by the standard library. Latin-1 maps each byte to the character of that code.
   UTF-16 reads code units in the stated order and joins surrogate pairs.

## Negative Logic (Prohibited Paths)

- Invalid UTF-8, an odd UTF-16 length, and an unpaired surrogate answer `None`; nothing is
  replaced with a substitute character. A lone low surrogate needs no check of its own:
  `charFromCode` refuses every surrogate code.

## Edge Cases

- Empty bytes decode to empty text in every encoding, and so does a byte-order mark alone.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangEnvironment/Domain/EncodingTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why refuse rather than substitute undecodable bytes?
  **A:** A secret with a substituted character is a wrong secret that authenticates nowhere, and
  the failure would surface far from its cause. _Rejected:_ U+FFFD replacement.

## Referenced by

[[CHANGELOG]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/Source]] · [[subsystems/Parsing]]
