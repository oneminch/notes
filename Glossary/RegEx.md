---
alias: Regular Expressions
---

- A Regular Expression (RegEx) is a syntax that specifies a certain search pattern for a piece of string.

## Basic Syntax

- **`^`**: start of string.
- **`$`**: end of string.
- **`*`**: zero or more occurrences of preceding pattern.
- **`+`**: one or more occurrences of preceding pattern.

- **`\d`**: any numeric character (`[0-9]`).
- **`\D`**: any character except numbers (`[^0-9]`).
- **`\w`**: any Latin character, all numbers and "_" (`[a-zA-Z0-9_]`).
- **`\W`**: any character except Latin characters, numbers and "_" (`[^a-zA-Z0-9_]`).
- **`\s`**: space only (`[ ]`).
- **`\S`**: any character except space (`[^ ]`).

- **`{n}`**: exactly `n` characters long.
- **`{n,}`**: at least `n` characters long.
- **`{n,m}`**: at least `n` and at most `m` characters long.

## Snippets

- **Match everything**: `[\s\S]*`
- **Optional Character**: `(a)?b`
- **Negative Look Ahead**: `a(?!b)`, `a` not followed by `b`.
- **Negative Look Behind**: `(?<!a)b`, `b` not preceded by `a`.
- **The Beginning of a File**: `^(?<!\n)`, `^(?<![\w\W])`, `^(?<![\s\S\r])`
