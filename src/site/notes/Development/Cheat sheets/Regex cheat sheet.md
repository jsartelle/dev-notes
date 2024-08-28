---
{"dg-publish":true,"dg-path":"Cheat sheets/Regex cheat sheet.md","permalink":"/cheat-sheets/regex-cheat-sheet/","tags":["language/javascript"]}
---


> [!note]
> Unless noted, all of the following is written with recent versions of JavaScript in mind. Different environments may support different regex features!

# General

- default to using the `v` (widely supported from September 2023 onwards) or `u` (for older browsers) flags to avoid bugs related to Unicode handling

# Groups

|             |                                                                    |
| ----------- | ------------------------------------------------------------------ |
| (?:...)     | non-capturing group                                                |
| (?\<name\>) | named capturing group (the angle brackets are part of the syntax!) |

## Lookarounds

- look for characters around the match, without adding those characters to the match
- break them them down into parts:
    - `?` marks a "special" group (like the ones above)
    - `<` for *behind* (optional)
    - `=` for *positive* or `!` for *negative*

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

### Change case (VSCode)

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

- `regexp.test(str)` - returns true/false
- `str.search(regexp)` - returns match index (-1 if not found)
- `regexp.exec(str)` - returns \[match, ...captureGroups\]
    - array has extra properties: `index`, `input`, `groups` (named capturing groups), `indices` (start/end indices of each capture group, only if `d` flag is used)
- `str.match(regexp)`
    - if `g` flag is **not** used, same as `regexp.exec`
    - if `g` flag is used, returns array of all matches
- `str.matchAll(regexp)` - returns iterator of `RegExp.exec` results
    - can turn it into an array using `[...str.matchAll(regexp)]`
