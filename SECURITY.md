# Security policy

## Reporting a vulnerability

Report a suspected vulnerability privately through GitHub's
[security advisory form](https://github.com/chrismichaelps/pudu-lang-environment/security/advisories/new),
or by email to <chrisperezsantiago1@gmail.com> with `SECURITY` in the subject.

Please do not open a public issue for a vulnerability. Include the package version, the `pudu`
version, the platform, and the smallest program that shows the problem. Never include a real
secret in a report; replace every value with a placeholder.

You can expect an acknowledgement within seven days and a decision on whether the report is
accepted within thirty.

## What is in scope

The package reads environment files that hold secrets and hands their values to the program that
asked. A report is in scope when a value escapes or a file is read that should not be:

- A value read from an environment file appearing in a `Problem`, a description, a rendering with
  `show`, or any text the package produces.
- An environment name that makes the layered files resolve outside the directory they were
  looked for in.
- A variable expansion that reads a variable it was not asked for, or that does not terminate.
- A file whose quoted value is never closed being loaded in part instead of refused.
- A loaded value overriding a variable the process already holds when overwriting is off.
- A variable handed to a child process that was not loaded or not asked for.

## What is not in scope

- A program that reveals a value it read on purpose, for example by printing `Variables.text`.
- Secrets left in an environment file checked into version control; keep `.env` files out of it.
- Vulnerabilities in the Pudu compiler or standard library; report those to
  [pudu-lang](https://github.com/chrismichaelps/pudu-lang/security).

## Supported versions

| Version | Supported |
| ------- | --------- |
| 0.1.x   | Yes       |
