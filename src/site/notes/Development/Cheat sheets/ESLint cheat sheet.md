---
{"dg-publish":true,"dg-path":"Cheat sheets/ESLint cheat sheet.md","permalink":"/cheat-sheets/es-lint-cheat-sheet/"}
---

# Comments

- Ignore a line:

```js
doStuff( ) // eslint-disable-line

// eslint-disable-next-line
doMoreStuff( )
```

- Ignore a block:
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
