---
{"dg-publish":true,"dg-path":"Cheat sheets/Jest.md","permalink":"/cheat-sheets/jest/"}
---


# CLI

## Run specific test

- pass a file pattern as the final argument
    - this would match `spec/index.spec.js`

```shell
jest spec/index
```

- you can also filter tests by test name (not file name)
    - this includes the surrounding describe blocks - ex. `jest -t "#describe testName"`

```shell
jest -t testName
```

## Show console logs

- add `--silent=false`

# Matchers

## Match partial objects, arrays, strings

```js
expect(mockFn).toHaveBeenCalledWith(
    expect.objectContaining({
        id: 123,
        name: expect.stringContaining('sofa'),
        model: expect.stringMatching(/A\d{3}/),
        colors: expect.arrayContaining(['red', 'blue']),
    })
)
```
