---
{"dg-publish":true,"dg-path":"Cheat sheets/ESLint.md","permalink":"/cheat-sheets/es-lint/","tags":["language/javascript"],"dg-note-properties":{"created":"2024-12-06T12:10:24-06:00","modified":"2025-06-16T22:25:57-05:00","tags":["language/javascript"]}}
---


# Comments

- Ignore a line:

```js
doStuff( ) // eslint-disable-line

// eslint-disable-next-line
doMoreStuff( )
```

- Ignore a block or entire file:
    - must be placed in block comments (`/* */`)

```js
/* eslint-disable */
function doStuff() {
badIndentation = true
}
/* eslint-enable */
```

- To disable specific rules, list them after the directive, separated with commas

```js
/* eslint-disable no-console, semi */
console.log('ignore this');
/* eslint-enable */

console.log('will still warn for semi'); // eslint-disable no-console
```

# CLI

- `--quiet`: only show errors
- `--fix`: attempt to fix auto-fixable problems
