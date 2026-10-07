---
type: domain
tags: [domain]
---

# Environment file

Lines of `KEY=value`. A blank line, a line whose first visible character is `#`, a line without
`=`, and a line whose key is empty are skipped. The key is the trimmed text before the first `=`;
with export syntax on, a leading `export` word is dropped from it.

A value whose first visible character is `'` or `"` is quoted: it runs to the next quote of the
same kind not preceded by an odd number of backslashes, across lines when needed, with the line
breaks kept. A quote that is never closed refuses the file. Inside it, an escaped quote and a
doubled backslash each stand for one character; a double-quoted value is expanded when expansion
is on, a single-quoted one never is. Text after the closing quote is ignored.

Any other value is the text after `=`, kept as written. With inline comments on, it ends before the
first `#` that follows a space or tab, and the space before it is dropped. With expansion on it is
expanded. With trimming on, every value loses its surrounding space last.

Parsing is in [[src/PuduLangEnvironment/Domain/Parser]].

## Referenced by

[[CHANGELOG]] · [[architecture/LANGUAGE]] · [[domain/_MOC]] · [[src/PuduLangEnvironment/Domain/Parser]] · [[subsystems/Parsing]]
