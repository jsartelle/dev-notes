---
{"dg-publish":true,"dg-path":"Cheat sheets/Regex cheat sheet.md","permalink":"/cheat-sheets/regex-cheat-sheet/","created":"","updated":""}
---


> [!note]
> Unless noted, all of the following is written with recent versions of JavaScript in mind. Different environments may support different regex features!

# Tokens

|         |                     |
| ------- | ------------------- |
| (?:...) | non-capturing group |

## Lookarounds

|          |                     |
| -------- | ------------------- |
| (?=...)  | positive lookahead  |
| (?!...)  | negative lookahead  |
| (?<=...) | positive lookbehind |
| (?<!...) | negative lookbehind |

## Lazy quantifiers

Add `?` after any quantifier to make it lazy (match as few characters as possible).

For example, given the text: *buffalo buffalo buffalo*

`/buffalo.+buffalo/` matches: *==buffalo buffalo buffalo==*

`/buffalo.+?buffalo/` matches: *==buffalo buffalo== buffalo*

## Substitutions

|     |                             |
| --- | --------------------------- |
| $&  | contents of match           |
| $\` | contents before match       |
| $'  | contents after match        |
| $1  | contents of capture group 1 |

### Change Case

> [!warning]
> These work in VSCode, but not in JavaScript!

|     |                    |
| --- | ------------------ |
| \\U | start uppercasing  |
| \\L | start lowercasing  |
| \\E | stop changing case |

ex. Title case Markdown headers:

```regex
Find:
(#\s*.)(.+)$

Replace:
\U$1\L$2
```

# JavaScript Methods

- `RegExp.test(str)` - returns true/false
- `String.search(regexp)` - returns match index (-1 if not found)
- `RegExp.exec(str)` - returns [match, ...captureGroups]
    - array has extra properties: `index`, `input`, `groups` (named capturing groups), `indices` (start/end indices of each capture group, only if `d` flag is used)
- `String.match(regexp)`
    - if `g` flag is **not** used, same as `RegExp.exec`
    - if `g` flag is used, returns array of all matches
- `String.matchAll(regexp)` - returns iterator of `RegExp.exec` results
    - access using `[...str.matchAll(regexp)]`
