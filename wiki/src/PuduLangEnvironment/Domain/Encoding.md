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
  | Ascii
  | Latin1
  | Utf16Le
  | Utf16Be
  | Utf32Le
  | Utf32Be

export fn decode(data: &Bytes, declared: Encoding) -> Option[Str]

export fn name(encoding: &Encoding) -> Str
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangEnvironment/Options]], [[src/PuduLangEnvironment/Source]].

## Algorithm

1. A UTF-8, UTF-32, or UTF-16 byte-order mark selects that encoding and is dropped; the UTF-32
   little-endian mark is looked for before the UTF-16 little-endian mark it begins with. Otherwise
   the declared encoding applies.
2. UTF-8 is checked by the standard library. ASCII refuses any byte from `0x80`. Latin-1 maps each
   byte to the character of that code. UTF-16 reads code units in the stated order and joins
   surrogate pairs. UTF-32 reads four-byte codes in the stated order.

## Negative Logic (Prohibited Paths)

- Invalid UTF-8, an odd UTF-16 length, and an unpaired surrogate answer `None`; nothing is
  replaced with a substitute character. A lone low surrogate needs no check of its own:
  `charFromCode` refuses every surrogate code.

## Edge Cases

- Empty bytes decode to empty text in every encoding, and so does a byte-order mark alone.
- A UTF-16 little-endian file whose first character is U+0000 reads as UTF-32 little-endian; the
  two marks cannot be told apart, and a key never starts with U+0000.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangEnvironment/Domain/EncodingTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why refuse rather than substitute undecodable bytes?
  **A:** A secret with a substituted character is a wrong secret that authenticates nowhere, and
  the failure would surface far from its cause. _Rejected:_ U+FFFD replacement.

- **Q:** Why these seven encodings?
  **A:** They are the encodings an environment file is written in on every platform Pudu targets,
  and each one's rules fit in a page; anything else is converted to UTF-8 before it is read.
  _Rejected:_ a pluggable decoder (a seam with one realistic implementation per encoding).

## Referenced by

[[CHANGELOG]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[src/PuduLangEnvironment/Options]] · [[src/PuduLangEnvironment/Source]] · [[subsystems/Parsing]]
