---
{"dg-publish":true,"dg-path":"Code review checklist.md","permalink":"/code-review-checklist/"}
---


```meta-bind-button
style: primary
label: Uncheck All
action:
  type: "regexpReplaceInNote"
  regexp: "- \\[x\\]"
  replacement: "- [ ]"
```

# General

- [ ] make sure there aren't any unwanted merges - for example, merging `sandbox` into a branch that will later be merged into `main`
- variable/function/export names should be clear & specific
    - [ ] consistent naming of related items
    - [ ] if names aren't self-explanatory, use [[Development/Notes/JavaScript#JSDoc\|JSDoc]] to add descriptions
    - [ ] prefer [[Development/Notes/JavaScript#Options objects with defaults\|options objects]] over long argument lists
- failure states:
    - [ ] make sure potential errors are handled
    - [ ] default to least access: instead of checking if a request should be **blocked**, block it by default and check if it should be **allowed**
    - [ ] don't assume env/config keys or request body parameters are present
    - [ ] wrap non-critical code like analytics in a try/catch so it doesn't break the application if it errors
    - [ ] throw Error objects instead of strings so the stack trace is available
- [ ] when comparing two values, account for cases where both values are missing
    - in the example below, if `process.env.API_KEY` is not set *and* `req.body.apiKey` is not provided, the happy path will be followed, which is probably not desired!
    - this could be fixed by checking `if (!apiKey || apiKey !== process.env.API_KEY)`

```js
/* this is bad code! */
const apiKey = req.body.apiKey

if (apiKey !== process.env.API_KEY) {
    throw new Error ('incorrect api key')
}

// happy path
```

- [ ] **don't forget to use `await` when necessary!!!**
    - ESLint [no-floating-promises](https://typescript-eslint.io/rules/no-floating-promises/) and [no-misused-promises](https://typescript-eslint.io/rules/no-misused-promises) can help catch these
- [ ] add `break` in switch cases
- [ ] use optional chaining (`?.`) when accessing properties on an object that could be undefined
- [ ] look for opportunities to parallelize: for example, if multiple async functions are called in a row, and the result only depends on one (or none) of them
- [ ] avoid awaiting non-critical things like analytics calls
- [ ] debounce buttons to prevent duplicate actions if the user clicks rapidly

# React

- [ ] [[Development/Notes/React#useMemo\|memoize]] complex calculations (but don't overdo it)
- [ ] wrap debounced functions or other higher-order functions in [[Development/Notes/React#useCallback\|useCallback]]

# Axios

- [ ] `res.status()` doesn't send the response immediately, you need an explicit `.send()` after
