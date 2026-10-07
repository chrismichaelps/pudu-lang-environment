<p align="center">
  <img src="public/pudu-lang-short.png" alt="Pudu" width="120">
</p>

<p align="center">
  <a href="https://www.pudu-lang.org/">Pudu</a> |
  <a href="https://www.pudu-lang.org/docs">Documentation</a> |
  <a href="https://www.pudu-lang.org/packages">Packages</a> |
  <a href="https://github.com/chrismichaelps/pudu-lang-environment/wiki">API docs</a> |
  <a href="CONTRIBUTING.md">Contributing</a>
</p>

# pudu-lang-environment

Environment files for Pudu. Keep a program's secrets in `.env` files outside version control, load
them over the process environment, and read them back as typed values. No refusal, description, or
rendering the package produces ever contains a value it read. Nothing is thrown: every refusal is a
`Problem` the caller can match on.

```pudu
import PuduLangEnvironment as Environment
import PuduLangEnvironment.Loader as Loader
import PuduLangEnvironment.Options as Options
import PuduLangEnvironment.Variables as Variables

fn main() -> Int {
  match Options.defaults().withStrict().load() {
    case Ok(variables) => {
      let port = Variables.int(&variables, "PORT")
      if port == Ok(8080) { 0 } else { 1 }
    }
    case Err(problem) => panic(Environment.describe(&problem))
  }
}
```

## Installing

```bash
pudu install @chrismichaelps/pudu-lang-environment
```

It needs [Pudu 0.1.3 or later](https://www.pudu-lang.org/download) and brings in
[`@chrismichaelps/pudu-lang-validator`](https://github.com/chrismichaelps/pudu-lang-validator) for
typed settings. Every module the package ships is `PuduLangEnvironment` or under it, so it takes no
module name from the program that installs it.

## Concepts

| Concept | Module | What it is |
| --- | --- | --- |
| Problem | `PuduLangEnvironment` | Why a load or read has no answer: `BlankPath`, `MissingFile`, `Unreadable`, `Undecodable`, `UnclosedQuote`, `NotFound`, `UnsafeEnvironmentName`, `InvalidOptions`, `Missing`, `Malformed`, or `Invalid`. It names keys, lines, and paths, never values. |
| Options | `Options` | What to read and how: a record with defaults, validated as a whole, and the same record changed through a fluent chain. |
| Source | `Source` | Where one file comes from: `File(path)`, `Text(label, text)`, or `Data(label, bytes)`. |
| Loader | `Loader` | `read` answers the merged map; `load` answers the settled `Variables`. `readOver` and `loadOver` take the process environment from the caller. |
| Variables | `Variables` | The loaded values settled over the process environment: `find`, `has`, `text`, `int`, `float`, `decimal`, `bool`, their `find…` forms, `secret`, `require`, `apply`, `describe`. |
| Binding | `Binding` | `derives Binding.Bind` reads a record's fields from variables; `derives Binding.Redacted` renders it with `@secret` fields hidden. |
| Settings | `Settings` | A bound record checked by a `pudu-lang-validator` validator. |

## Options

| Option | Default | Chain |
| --- | --- | --- |
| `strict` | `false`: a missing or unreadable source is skipped and recorded in `skipped` | `withStrict`, `withoutStrict` |
| `files` | `[".env"]`, read in order, relative to `directory` | `withFiles` |
| `sources` | none; text or bytes read instead of files | `withSources` |
| `encoding` | `Utf8`; also `Latin1`, `Utf16Le`, `Utf16Be`; a byte-order mark always wins | `withEncoding` |
| `trimValues` | `false` | `withTrimValues`, `withoutTrimValues` |
| `overwrite` | `true`: later layers win, and loaded values replace process variables | `withOverwrite`, `withoutOverwrite` |
| `probe` | `None`; `Some(n)` looks for `.env` in `directory` and up to `n` parents | `withProbe` (4 levels), `withProbeLevels`, `withoutProbe` |
| `exportSyntax` | `false`; reads `export KEY=value` | `withExportSyntax`, `withoutExportSyntax` |
| `inlineComments` | `true`; `KEY=value # note` ends before the `#` | `withInlineComments`, `withoutInlineComments` |
| `expansion` | `false`; expands `$NAME`, `${NAME}`, `${NAME:-default}`, `${NAME-default}` | `withExpansion`, `withoutExpansion` |
| `cascade` | `false`; reads `.env`, `.env.local`, `.env.<name>`, `.env.<name>.local` | `withCascade`, `withEnvironment(name)`, `withoutCascade` |
| `environmentName` | `None`; else `PUDU_ENV`, then `PROFILE` | `withEnvironment(name)` |
| `directory` | `"."`, the working directory | `inDirectory` |

Every conflict — files with probing or the cascade, sources with files, probing, or the cascade, a
negative probe depth — is reported at once as `InvalidOptions` when the load starts.

## The file format

```bash
# A comment line; indented comments are comments too.
PORT=8080
HOST = localhost              # an inline comment after a space
export REGION=eu-west         # with export syntax on
GREETING='Hello, $USER'       # single quotes: never expanded
URL="https://${HOST}:${PORT}" # double quotes: expanded when expansion is on
PRIVATE_KEY="-----BEGIN KEY-----
MIIEvQIBADANBg...
-----END KEY-----"            # a quoted value spans lines
```

A quote that is never closed refuses the file, even when lenient, so a program never starts on half
a secret. A value is expanded once, when it is assigned, from the values assigned before it and
then the process environment.

## Typed settings

```pudu
import Std.App.Secret as Secret
import PuduLangValidator.Rule as Rule
import PuduLangValidator.Rules.Number as NumberRule
import PuduLangValidator.Validator as Validator
import PuduLangEnvironment.Binding as Binding
import PuduLangEnvironment.Settings as Settings

type Server = {
  port: Int,                                     // reads PORT
  @env("DB_URL") @secret database: Secret.Secret,
  @default("info") logLevel: Str,                // reads LOG_LEVEL, else "info"
  workers: Option[Int]                           // may be unset
} derives Binding.Bind, Binding.Redacted

fn rules() -> Validator.Validator[Server] {
  Validator.add(Validator.create(), Rule.build(NumberRule.inclusiveBetween(Rule.ruleFor("port", |given: Server| given.port), 1024, 65535)))
}

// Settings.bind(&variables, &rules()) answers the Server, or Missing with every unset variable,
// Malformed with the first that does not convert, or Invalid with each refused property and code.
// server.redacted() renders Server{port: 8443, database: [REDACTED], ...}.
```

## Keeping secrets secret

- Loading never writes the process environment; the `Variables` it answers are the only place the
  values live, for as long as the caller keeps them.
- `show(variables)` renders `<fn>` in place of the values, and `Variables.describe` lists counts and
  sources only.
- `Variables.secret` and `Secret.Secret` fields wrap a value so it renders as `[REDACTED]` through
  `Secret.redact`.
- `Variables.apply` gives a child process only the keys the load applied; with
  `Process.launch(...).isolated()` the child sees nothing else.
- Keep `.env` and `.env.*.local` out of version control.

## Examples

`examples/` holds runnable programs: `QuickStart`, `Layered`, `TypedSettings`, `ChildProcess`, and
`InMemory`.

```bash
pudu run examples/QuickStart.pudu
```

## Developing

```bash
pudu install --locked                                  # the validator dependency
pudu test test                                         # every suite
pudu fmt --check src test tools examples && pudu lint src test tools examples
pudu run tools/Mutate.pudu --domain --suites test/PuduLangEnvironment/Domain --threshold 100
```

The design lives in the [wiki vault](wiki/00-INDEX.md): one page per source file, the decisions
behind the format and the secrecy rules, and the Pudu grammar rules the code follows.

## License

[Apache License 2.0](LICENSE).
