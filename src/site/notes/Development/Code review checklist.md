---
{"dg-publish":true,"dg-path":"Code review checklist.md","permalink":"/code-review-checklist/"}
---


# Code

## General

- look for unwanted merges (ex. merging `sandbox` into a branch that will later be merged into `main`)
- start by looking at database migrations or test changes

## Structure

- variable/function/export names should be clear & specific
    - related items should be named consistently
- prefer [[Development/Cheat sheets/JavaScript#Options objects with defaults\|options objects]] over long argument lists
- don't assume env/config keys or request body parameters are present
- make sure switch cases have `break` or `return`
- debounce inputs or disable them during loading
- look for opportunities to parallelize: ex. if multiple async functions are called in a row, and the result only depends on one (or none) of them
- avoid repeated iteration: ex. instead of calling `map()` on the same array multiple times, call it once and return a result object for each array item
- wrap non-critical code like analytics in a try/catch, so it doesn't break the application if it errors
- avoid awaiting non-critical code like analytics

## Security & error handling

- default to least access: instead of checking if a request should be **blocked**, block it by default and check if it should be **allowed**
- check `!` type assertions carefully, and try to avoid them whenever possible
- throw Error objects instead of strings so the stack trace is available
- when comparing values, account for cases where both values are missing
    - in the example below, if `process.env.API_KEY` is not set *and* `req.body.apiKey` is not provided, the happy path will be followed, which is probably not desired!
    - this could be fixed by checking `if (!apiKey || apiKey !== process.env.API_KEY)`

```js
/* this is bad code! */
const apiKey = req.body.apiKey

if (apiKey !== process.env.API_KEY) {
    throw new Error ('incorrect api key')
}

// happy path, will run when it shouldn't
```

# React

- [[Development/Cheat sheets/React#useMemo\|memoize]] complex calculations, but don't overdo it (memoization has a performance cost too)
    - same with [[Development/Cheat sheets/React#useCallback\|useCallback]] for callbacks
