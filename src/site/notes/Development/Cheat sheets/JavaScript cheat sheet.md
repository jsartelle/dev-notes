---
{"dg-publish":true,"dg-path":"Cheat sheets/JavaScript cheat sheet.md","permalink":"/cheat-sheets/java-script-cheat-sheet/"}
---


# Arrays

> [!warning]
> Remember that `sort` and `reverse` mutate the original array!

## Filter out falsy values

```js
array.filter(Boolean)
```

## Sort array of numbers

```js
array.sort((a, b) => a - b)
```

## Sort array of objects by a string property

```js
people = people.sort((a, b) => a.name.localeCompare(b.name))
```

## Sort array of objects by date (oldest first)

- Also works for `moment` objects

```js
people = people.sort((a, b) =>
    new Date(a.birthday) - new Date(b.birthday)
)
```

## Shuffle (in place)

```js
function shuffleArray(array) {
    for (var i = array.length - 1; i > 0; i--) {
        var j = Math.floor(Math.random() * (i + 1));
        var temp = array[i];
        array[i] = array[j];
        array[j] = temp;
    }
}
```

## Language-aware list formatting with Intl.ListFormat

```js
const fruit = ['apples', 'bananas', 'pears']

const formatter = new Intl.ListFormat('en', { style: 'long', type: 'conjunction' })

formatter.format(fruit) // "apples, bananas, and pears"
```

- Styles:
    - `long`: "A, B, and C"
    - `short`: "A, B, C"
    - `narrow`: "A B C"
- Types:
    - `conjunction`: "A, B, and C"
    - `disjunction`: "A, B, or C"
    - `unit`: "A, B, C"

# Objects

## Return an object from an an arrow function expression

- Wrap the object in parentheses

```js
// this won't work, because {} creates a block for the function body
const getObject = () => { a: 1, b: 2 }

// but this does
const getObject2 = () => ({ a: 1, b: 2 })
```

# Destructuring

## Destructure nested objects

```js
const user = {
	first_name: 'Bob',
	address: {
		street: '123 Fake St',
		city: 'New York',
		state: 'NY'
	}
}

const {
	first_name,
	address: {
		street: address_street // renamed
	} = {} // default value to avoid error if address is undefined
} = user
// this will still error if address is null!

console.log(address_street)
```

## Destructuring assignment to existing variables

- When using destructuring without a declaration, wrap the whole statement in parentheses and add a semicolon beforehand

```js
let name, address
;({ name, address } = person)
```

## Options objects with defaults

- Set default values separately for each property:

```js
function test({ name = 'banana', color = 'yellow' } = {}) {
	console.log(`${name}s are ${color}`)
}

test({ name: 'apple', color: 'red' })  // apples are red
test({ name: 'lime' })                 // limes are yellow
test({})                               // bananas are yellow
test()                                 // bananas are yellow
```

- Set a default value for the entire object
    - In TypeScript this will error if you provide a partial object

```js
function test2(options = { name: 'banana', color: 'yellow' }) {
	console.log(`${options.name}s are ${options.color}`)
}

test2({ name: 'apple', color: 'red' }) // apples are red
test2({ name: 'lime' })                // limes are undefined (errors in TS)
test2({})                              // undefineds are undefined (errors in TS)
test2()                                // bananas are yellow
```

# Numbers

## Random number in range (exclusive)

```js
Math.random() * (max - min) + min;
```

## Random integer in range (inclusive)

```js
// assuming min and max are integers
return Math.floor(Math.random() * (max - min + 1) + min)

// if min is 0 this simplifies to
return Math.floor(Math.random() * (max + 1))
```

# Strings

## Use a function for String.replace

```js
string.replace(/%%(\w+)%%/, (match, p1, p2 /*...pN*/, offset, string, groups) => {
    // groups is an object of named capturing groups
    return replacement
})
```

## Copy text to clipboard

```js
await navigator.clipboard.writeText('text')
```

# Debugging

## Log objects with indentation

```js
console.log(JSON.stringify(object, null, '  '))
```

## Console timers

- `timeLog` and `timeEnd` both log elapsed time to the console (`timeLog` is optional)
- timer name must match between calls
    - prefix with something like `[timer]` to make it easier to search for

```js
function longRunningFunction() {
    const timerName = '[timer] longRunningFunction'
    console.time(timerName)
    doSomething()
    console.timeLog(timerName)
    doSomethingElse()
    console.timeEnd(timerName)
}
```

# Other

## Operator precedence

- `&&` has a higher precedence than `||`
    - `a || b && c` is the same as `a || (b && c)`
- **however**, `&&` and `||` short circuit, so if `a` is truthy, `(b && c)` won't be evaluated even though it's in parentheses
- math operators follow PEMDAS (`%` has the same precedence as `/`)

## Use expressions in switch statements

```js
switch(true) {
    case !user:
        throw new Error('User not provided')
    case !user.name:
        throw new Error('User name not provided')
    case user.name.length < 5:
        throw new Error('User name must be at least 5 characters')
    case typeof user.name !== 'string':
        throw new Error('User name is not a string')
}
```

## Media queries

```js
const mediaQuery = window.matchMedia("(orientation: portrait)")
const isPortrait = mediaQuery.matches
mediaQuery.addEventListener('change', adjustLayout)
```

## Resize observer

```js
const observer = new ResizeObserver((entries) => {
    entries.forEach(entry => {
        console.log('width', entry.contentBoxSize[0].inlineSize)
        console.log('height', entry.contentBoxSize[0].blockSize)
    })
})
observer.observe(document.querySelector('main'))
```

# JSDoc

```js
/** @type {string} */
```

- optional parameter names go in brackets

```js
/**
@param {string} [name] Description goes here
*/
```

- import a type and give it an alias

```js
/** @typedef {import('fruit/types').Apple} apple */

/** @type {apple} */
const redApple = new Apple('red')

/** @type {apple} */
const greenApple = new Apple('green')
```

# See also

- [[Development/Cheat sheets/TypeScript cheat sheet\|TypeScript cheat sheet]]
