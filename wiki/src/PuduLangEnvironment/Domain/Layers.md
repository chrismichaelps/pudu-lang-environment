---
type: module
path: "@root/src/PuduLangEnvironment/Domain/Layers.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.6
depth_status: MEDIUM
tags: [module, domain]
aliases: [PuduLangEnvironment.Domain.Layers]
---

# PuduLangEnvironment.Domain.Layers

## Purpose

Entries, which assignment is kept, the merge of layers, and the settled view.

## Interface

### Signatures

```pudu
export type Entry = { key: Str, value: Str }

export type Settled = { values: Map[Str, Str], applied: Array[Str] }

export fn keep(known: &Map[Str, Str], key: Str, value: Str, overwrite: Bool) -> Map[Str, Str]

export fn merge(layers: &Array[Array[Entry]], overwrite: Bool) -> Map[Str, Str]

export fn settle(loaded: &Map[Str, Str], process: &Map[Str, Str], overwrite: Bool) -> Settled
```

### Linkage

- **Requires:** std only.
- **Consumed by:** [[src/PuduLangEnvironment/Domain/Parser]], [[src/PuduLangEnvironment/Loader]], [[src/PuduLangEnvironment/Variables]].

## Algorithm

1. `keep` stores a value when overwriting or when the key is new.
2. `merge` keeps every entry of every layer in order.
3. `settle` starts from the process map and stores each loaded value when overwriting or when the
   process value is unset or empty; `applied` lists the stored keys in sorted order.

## Negative Logic (Prohibited Paths)

- Without overwriting, a non-empty process variable is never replaced.

## Edge Cases

- Without overwriting, the first assignment of a key wins across files and within one.

## Depth

DEPTH 0.6 (MEDIUM). Tested by `test/PuduLangEnvironment/Domain/LayersTest.pudu`; mutation-tested.

## Grill Log

- **Q:** Why may a load replace an empty process variable even without overwriting?
  **A:** An empty variable is absent to every reader ([[src/PuduLangEnvironment/Variables]]), so
  keeping it would discard a value for nothing. _Rejected:_ "set" meaning present at all.

- **Q:** Why is `applied` not sorted explicitly?
  **A:** A `Map` answers its entries in key order, so the keys are stored sorted as they are met; a
  second sort could not change the answer, and mutation testing reported it as such.
  _Rejected:_ `List.sortBy` over the applied keys.

## Referenced by

[[CHANGELOG]] · [[domain/Layer]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[src/PuduLangEnvironment/Domain/_MOC]] · [[src/PuduLangEnvironment/Loader]] · [[src/PuduLangEnvironment/Variables]] · [[subsystems/Loading]]
