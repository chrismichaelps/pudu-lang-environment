---
type: language
tags: [architecture]
aliases: [Vocabulary]
---

# Architecture vocabulary

- **Module** — one `.pudu` file and its mirrored page under `wiki/src/`.
- **Interface** — a module's exported signatures; what callers may rely on.
- **Depth** — how much behaviour an interface hides relative to its size. Recorded per page.
- **Seam** — a boundary where one implementation replaces another without editing callers:
  [[seams/World]].
- **Environment file** — text of `KEY=value` assignments. See [[domain/EnvironmentFile]].
- **Source** — where one environment file comes from: a path, text, or bytes.
- **Entry** — one assignment a file makes, in file order.
- **Layer** — the entries of one source; later layers override earlier ones when overwriting is on.
  See [[domain/Layer]].
- **Expansion** — replacing `$NAME` and `${NAME}` with a known value. See [[domain/Expansion]].
- **Discovery** — choosing which files to read: given, probed for, or cascaded. See
  [[domain/Discovery]].
- **Process environment** — the variables the program was started with, read once per load.
- **Variables** — the loaded values settled over the process environment. See
  [[domain/Variables]].
- **Strict / lenient** — whether a missing or unreadable source refuses the load or is skipped
  and recorded.
- **Binding** — reading a record's fields from variables by its derived `Bind`; `@secret` fields
  render hidden through `Redacted`. See [[src/PuduLangEnvironment/Binding]].
- **Settings** — a bound record a validator accepted. See [[src/PuduLangEnvironment/Settings]].
- **Problem** — why something could not be read or answered; never holds a value.

## Referenced by

[[architecture/_MOC]]
