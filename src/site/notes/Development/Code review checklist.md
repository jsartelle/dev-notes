---
{"dg-publish":true,"dg-path":"Notes/Code review checklist.md","permalink":"/notes/code-review-checklist/"}
---


- make sure there aren't any unwanted merges (ex. merging `sandbox` into a branch that will later go into `main`)
- **don't forget to use `await`!!!**
- variable/method/export names should be clear & specific
    - if argument names aren't self-explanatory, use [JSDoc](https://jsdoc.app/about-getting-started.html) to add descriptions
    - prefer [[Development/Cheat sheets/JavaScript cheat sheet#Options objects with defaults \|options objects]] over long argument lists
- failure states:
    - error handling
    - default to least access
    - handle missing env/config keys or request body parameters
- when comparing two values, account for cases where both values may be null/undefined
    - in the example below, if `process.env.API_KEY` is not set, the happy path will be followed if `apiKey` is not provided in the body, which is probably not desired!
    - this could be fixed by checking `if (!apiKey || apiKey !== process.env.API_KEY)`

```js
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
