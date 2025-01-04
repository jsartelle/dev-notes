---
{"dg-publish":true,"dg-path":"Notes/Code review checklist.md","permalink":"/notes/code-review-checklist/"}
---


- make sure there aren't any unwanted merges - for example, merging `sandbox` into a branch that will later go into `main`
- **don't forget to use `await` when necessary!!!**
    - use ESLint [no-floating-promises](https://typescript-eslint.io/rules/no-floating-promises/)
    - VSCode inlay hints can help too (ex. `typescript.inlayHints.variableTypes.enabled` and `typescript.inlayHints.functionLikeReturnTypes.enabled`)
- look for opportunities to parallelize things - ex. if multiple async functions are called in a row, and the result only depends on one (or none) of them
- avoid awaiting non-critical things like analytics, and wrap them in their own try/catch so they don't cause an error if they fail
- variable/method/export names should be clear & specific
    - if argument names aren't self-explanatory, use [JSDoc](https://jsdoc.app/about-getting-started.html) to add descriptions
    - prefer [[Development/Notes/JavaScript#Options objects with defaults\|options objects]] over long argument lists
- failure states:
    - error handling
    - default to least access
        - instead of checking if a request should be **blocked**, block by default and check if it should be **allowed**
    - handle missing env/config keys or request body parameters
- throw Error objects instead of strings so that stack trace is available
- when comparing two values, account for cases where both are null/undefined
    - in the example below, if `process.env.API_KEY` is not set, the happy path will be followed if `req.body.apiKey` is not provided, which is probably not desired!
    - this could be fixed by checking `if (!apiKey || apiKey !== process.env.API_KEY)`

```js
/* this is bad code! */
const apiKey = req.body.apiKey

if (apiKey !== process.env.API_KEY) {
    throw new Error ('incorrect api key')
}

// happy path
```

- add `break` in switch statements
- use optional chaining (`?.`) when accessing properties on an object that could be undefined
- debounce buttons to prevent duplicate actions if the user clicks rapidly
- Vue:
    - use `this` when accessing component data in `<script>` (with options API), but not in `<template>`
- Axios:
    - `res.status()` doesn't send the response immediately, you need an explicit `.send()` after
