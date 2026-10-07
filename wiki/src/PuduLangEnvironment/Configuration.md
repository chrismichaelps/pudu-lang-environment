---
type: module
path: "@root/src/PuduLangEnvironment/Configuration.pudu"
fidelity: Active
grammar: "[[grammar/pudu]]"
depth_score: 0.4
depth_status: MEDIUM
tags: [module]
aliases: [PuduLangEnvironment.Configuration]
---

# PuduLangEnvironment.Configuration

## Purpose

Loaded variables layered over the standard library's application configuration.

## Interface

### Signatures

```pudu
export trait Overlaying {
  fn withVariables(self: &Self, variables: &Variables.Variables) -> Self
}

impl Overlaying for Config.Config
```

### Linkage

- **Requires:** `Std.App.Config`, [[src/PuduLangEnvironment/Domain/Keys]], [[src/PuduLangEnvironment/Variables]].
- **Consumed by:** package users and the suites.

## Algorithm

1. For each setting the configuration declares, read the variable its key names
   ([[src/PuduLangEnvironment/Domain/Keys]]: `server.port` reads `SERVER_PORT`).
2. A variable that is set and not empty replaces the setting, recorded as coming from the
   environment, so `Config.sourceOf` reports where the value came from.

## Negative Logic (Prohibited Paths)

- A variable no setting declares never becomes a setting, so the whole environment does not leak
  into configuration.

## Edge Cases

- An empty variable leaves the declared value, as an empty variable is absent everywhere else.
- The profile and every other field of the configuration are kept.

## Depth

DEPTH 0.4 (MEDIUM). Tested by `test/PuduLangEnvironment/ConfigurationTest.pudu`.

## Grill Log

- **Q:** Why a trait method on `Config.Config` rather than a function?
  **A:** Configuration is built through the standard `Layering` chain; `withVariables` reads as one
  more layer in it: `Config.declaring(&defaults).withVariables(&variables).withArguments()`.
  _Rejected:_ a free function that breaks the chain.
- **Q:** Why the standard library's key spelling?
  **A:** A program moving from `withEnvironment()` to `withVariables` keeps every variable name.
  _Rejected:_ the upper snake case of record fields (splits `serverPort`, not `server.port`).

## Referenced by

[[CHANGELOG]] · [[architecture/_MOC]] · [[handoffs/2026-10-07-initial-package]] · [[src/PuduLangEnvironment/Domain/Keys]] · [[src/PuduLangEnvironment/_MOC]]
