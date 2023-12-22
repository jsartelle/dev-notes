---
{"dg-publish":true,"dg-path":"Cheat sheets/JavaScript cheat sheet.md","permalink":"/cheat-sheets/java-script-cheat-sheet/"}
---


# Arrays

> [!warning]
> Remember that `sort`, `reverse`, and `splice` mutate the original array! (see [[Development/Cheat sheets/JavaScript cheat sheet#Non-mutating methods\|#Non-mutating methods]])

## Filter out falsy values

```js
array.filter(Boolean)
```

## Array with range of numbers

```js
// [0, 1, 2, 3, 4]
[...Array(5).keys()]
```

```js
// [1, 2, 3, 4, 5]
[...Array(5).keys()].map(v => v + 1)
```

## Sort array of numbers

```js
array.sort((a, b) => a - b)
```

## Sort array of objects by a string property

```js
people.sort((a, b) => a.name.localeCompare(b.name))
```

## Sort array of objects by date (oldest first)

- Also works for `moment` objects

```js
people.sort((a, b) =>
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

## Array.at

- Returns the item at an index, but supports negative indices

```js
const arr = ['apple', 'banana', 'orange', 'mango']
console.log(arr.at(1)) // banana
console.log(arr.at(-1)) // mango
```

## Non-mutating methods

- `toSorted`, `toReversed`, and `toSpliced` are the *copying* versions of `sort`, `reverse` and `splice` (they return a new array)
- `with` is the copying version of using bracket notation
    - `with` also supports negative indices

```js
const foo = [1, 2, 3]
foo[1] = 4
console.log(foo) // [1, 4, 3]

const bar = [1, 2, 3]
const xyz = bar.with(1, 4)
console.log(bar) // [1, 2, 3]
console.log(xyz) // [1, 4, 3]
```

## Language-aware sorting with Intl.Collator

```js
console.log(['Z', 'a', 'z', 'ä'].sort(new Intl.Collator('de').compare));
// Expected output: Array ["a", "ä", "z", "Z"]
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

## Use a function with String.replace

```js
string.replace(
    /(hello)\s+(world)/,
    (match, p1, p2, /*...pN,*/ offset, string, groups) => {
    // p1, p2, etc. are capture groups, as many as there are in the regex
    // groups is an object with the named capturing groups, or undefined if there are none
    
    // do whatever you want here
    return replacement
})
```

## Splice function for strings

```js
function spliceString(input, start = 0, deleteCount = 0, additional = '') {
  return input.slice(0, start) + additional + input.slice(start + deleteCount)
}
```

## Copy text to clipboard

```js
await navigator.clipboard.writeText('text')
```

# Debugging

## Quickly log variables with labels

- Use object property shorthand to quickly log variables along with their names

```js
const name = 'Sam'
const age = 32

console.log({ name, age }) // logs {name: 'Sam', age: 32}
```

## Log objects with indentation

```js
console.log(JSON.stringify(object, null, '  '))
```

## Log objects or arrays as a table

```js
console.table({ foo: 'bar', obj: { a: 1, b: 2 }})
```

| (index) | Value | a   | b   |
| ------- | ----- | --- | --- |
| foo     | 'bar' |     |     |
| obj     |       | 1   | 2   |

## Launch debugger with a countdown

- Good for capturing things like toasts that are triggered by user interaction

```js
setTimeout(() => { debugger }, 3000)
```

## Console timers

- `timeLog` and `timeEnd` both log elapsed time to the console (`timeLog` is optional)
- the timer name must match between calls
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
    - but don't rely on this, use parentheses to make your intention clearer
- `&&` and `||` **short circuit**
    - in `a && (b + c)`, if `a` is falsy, `(b + c)` won't be evaluated even though it's in parentheses
- math operators follow PEMDAS (`%` has the same precedence as `/`)

## Assignment using switch statements

- You can assign a variable from a switch statement by wrapping it in an arrow function and immediately calling it

```js
const screenName = (() => {
	switch (index) {
		case 0:
			return 'Home'
		case 1:
			return 'Profile'
		case 2:
			return 'Settings'
		default:
			return 'Unknown'
	}
})()
```

## Use expressions in switch statements

- You can test expressions in `switch` statements by comparing against `true`

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

## `Element.closest`

- finds the closest element in an element's ancestor tree (including itself) that matches the selector

```html
<div class="apple">
    <div class="orange outer">
        <div class="orange inner"></div>
        <div class="banana"></div>
    </div>
</div>
```

```js
document.querySelector('.banana').closest('.orange')
// matches .orange.outer (.orange.inner isn't in the element's tree)

document.querySelector('.banana').closest('.banana')
// matches the element it was called on
```

## Media queries

```js
const mediaQuery = window.matchMedia("(orientation: portrait)")
const isPortrait = mediaQuery.matches
mediaQuery.addEventListener('change', adjustLayout)
```

## Restart CSS animation

- Checking `element.offsetHeight` triggers reflow, which is necessary for the animation change to be applied

```js
element.classList.remove('animated')
element.offsetHeight
element.classList.add('animated')
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

- to make a param optional put the *name* in square brackets
- when using TypeScript, you can leave out the type if it's declared in the function signature, and just use JSDoc for adding descriptions

```js
/**
@param {string} param1 Description goes here
@param {boolean} [param2] This param is optional
*/
```

- define a reusable object type
    - can also be done in a standalone comment (not attached to a function)

```js
/**
 * @typedef {Object} leagueParam
 * @property {string} league
 * @property {string[]} team
 * @property {string} rank
 * @property {string} division
 * @property {string} conference
 * @returns {leagueParam}
 */
```

- type a tuple

```js
/**
 * @typedef {string} league
 * @typedef {string[]} team - array of team names
 * @typedef {string[]} rank - array of rank names
 * @typedef {string[]} division - array of division names
 * @typedef {string[]} conference - array of conference names
 * @returns { [league, team?, rank?, division?, conference?] }
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

- define function overloads

```js
/**
 * @overload
 * @param {string} param
 * @param {false} allowsMultiple
 * @returns {string}
 */

/**
 * @overload
 * @param {string} param
 * @param {true} allowsMultiple
 * @returns {string[]}
 */

/* returns a string if allowsMultiple is false, and an array of strings if allowsMultiple is true */
function getParamValue(param, allowsMultiple) { ... }
```

# See also

- [[Development/Cheat sheets/TypeScript cheat sheet\|TypeScript cheat sheet]]
