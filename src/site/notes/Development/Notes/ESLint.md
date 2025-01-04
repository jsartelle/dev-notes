---
{"dg-publish":true,"dg-path":"Notes/Notes/ESLint.md","permalink":"/notes/notes/es-lint/","tags":["language/javascript"]}
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
