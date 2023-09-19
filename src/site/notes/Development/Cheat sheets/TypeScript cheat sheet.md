---
{"dg-publish":true,"dg-path":"Cheat sheets/TypeScript cheat sheet.md","permalink":"/cheat-sheets/type-script-cheat-sheet/"}
---


# Types vs. Interfaces

> [!tip]
> Interfaces are simpler to reason with. Prefer interfaces unless you need the added power of types.

- Interfaces can be added to after creation through [[Development/Cheat sheets/TypeScript cheat sheet#Declaration merging\|declaration merging]], types can't

# Tuples

- An array with a fixed number of elements, and a fixed type for each element position
- `string[]` creates an array which can hold any number of string values, `[string]` creates a tuple which holds a single string
    - `[string][]` is an array that holds any number of `[string]` tuples, `[string[]]` is a tuple which holds a single `string[]` array
- Tuple elements can be optional: `[number, number?, number?]` is a tuple that can hold between 1 and 3 numbers

```ts
type NameAndAge = [string, number]

const person: NameAndAge = ['Bob', 42]
const [name, age] = person // destructuring
```

# Enums

```ts
enum Response { Yes, No }
// equivalent to
enum Response { Yes = 0, No = 1}

handleResponse(Response.Yes)
```

- Adjust where the auto-increment starts:

```ts
enum Response { Yes = 1, No }
// equivalent to
enum Response { Yes = 1, No = 2}
```

- Numeric enums get a reverse mapping between keys and values:

```ts
enum Colors { Red = 1, Blue }

console.log(Colors.Red) // 1
console.log(Colors[1]) // "Red"
```

- String enums **do not** get a reverse mapping, but their values can be easier to understand at runtime:

```ts
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}

console.log(Direction.Up) // "UP"
console.log(Direction["UP"]) // error
```

- To get a union type of all enum **keys**, use `keyof typeof`

```ts
enum LogLevel {
  ERROR,
  WARN,
  INFO,
  DEBUG,
}

type LogLevelStrings = keyof typeof LogLevel
// equivalent to
type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG'
```

## Computed members

- Enum values that cannot be evaluated at compile time are *computed* (vs. *constant*)
    - Computed values must come after constant values

```ts
enum Test {
    // all these are constant
    A = 1,
    B = 2,
    C = A + B,
    // this is computed
    Z = "aaaaa".length
}
```

## Const enums

- Enums with **only constant** members can be marked as `const` to boost performance - they will be removed during compilation and their values will be inlined

```ts
const enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}

let skyDirection = Direction.Up

// everything above compiles to:
let skyDirection = "UP"
```

# Discriminating unions

- unions of multiple object types with some common properties

```ts
type NetworkLoadingState = {
  state: 'loading'
}
type NetworkFailedState = {
  state: 'failed'
  code: number
}
type NetworkSuccessState = {
  state: 'success'
  response: NetworkResponse
}

type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState

function logState(requestState: networkState) {
    // requestState.state is safe to access because it exists
    // on every type in the union 
    console.log(`Request status: $(requestState.state)`)

    switch (requestState.state) {
        case 'failed':
            // this narrows the type of requestState to NetworkFailedState
            console.log(`Error code: $(requestState.code)`)
            break
        /* ... other cases */
    }
}
```

# `satisfies`

- Lets you check that an expression matches a type, without changing the expression's type

```ts
type Colors = "red" | "green" | "blue";
type RGB = [red: number, green: number, blue: number];
```

- Without `satisfies`:

```ts
const palette: Record<Colors, string | RGB> = {
    red: [255, 0, 0],
    green: "#00ff00",
    bleu: [0, 0, 255] // this typo will be caught
};

const redComponent = palette.red.at(0); // but this will error, because the type of palette.red has changed to string|RGB
```

- With `satisfies`:

```ts
const palette = {
    red: [255, 0, 0],
    green: "#00ff00",
    bleu: [0, 0, 255] // the typo is caught
} satisfies Record<Colors, string | RGB>;

// these both work because the properties keep their types
const redComponent = palette.red.at(0);
const greenNormalized = palette.green.toUpperCase();
```

## Removing modifiers

- Remove `readonly` or `?` modifiers using `-`

```ts
// Removes 'readonly' attributes from a type's properties
type Mutable<Type> = {
  -readonly [Property in keyof Type]: Type[Property];
};
 
type LockedAccount = {
  readonly id: string;
  readonly name: string;
};
 
type UnlockedAccount = Mutable<LockedAccount>;

// Removes 'optional' attributes from a type's properties
type Concrete<Type> = {
  [Property in keyof Type]-?: Type[Property];
};
 
type MaybeUser = {
  id: string;
  name?: string;
  age?: number;
};
 
type User = Concrete<MaybeUser>;
```

# Typing `for...in` loops

```ts
const Colors = {
    red: '#ff0000',
    green: '#00ff00',
    blue: '#0000ff',
}

let key: keyof typeof Colors
for (key in Colors) {
    console.log(`The hex code of ${key} is ${Colors[key]}`)
}
```

# Type Manipulation

## Intersection types

```ts
type Foo = { a: number }
interface Bar { b: number }

// you can combine types (including object literals) & interfaces, and the result will be a type
type Baz = Foo & Bar & {
  c: number
}
```

- Properties with the same name and different object types are merged:

```ts
type Foo = { x: { y: number } }
type Bar = { x: { z: string } }

type Baz = Foo & Bar

const x: Baz = { x: { y: 123, z: 'abc' } }
```

## Extending interfaces

```ts
interface Foo { a: number }
interface Bar { b: number }

interface Baz extends Foo, Bar {
  c: number
}
```

- Properties with the same name and different object types cause an error:

```ts
interface Foo { x: { y: number } }
interface Bar { x: { z: string } }

interface Baz extends Foo, Bar // error: Named property 'x' of types 'Foo' and 'Bar' are not identical
```

## Declaration merging

- Used to add new properties to an existing interface

```ts
interface Book {
  title: string
  author: string
}

interface Book {
  year: number
}
```

## Combine types (intersection type)

```ts
interface Identity {
    id: number;
    name: string;
}

interface Contact {
    email: string;
    phone: string;
}

type Employee = Identity & Contact
```

## Get type from interface (indexed access types)

```ts
interface Book {
    genre: 'comedy' | 'drama' | 'mystery'
}

interface Movie {
    genre: Book['genre']
}
```

## Omit keys from a type

```ts
type Cube = {
    length: number
    width: number
    depth: number
}

type Square = Omit<Cube, 'depth'>
type Line = Omit<Cube, 'width' | 'depth'>
```

## Union type from interface properties (`keyof`)

```ts
interface Person {
    first_name: string
    last_name: string
    age: number
}

// 'first_name' | 'last_name' | 'age'
type PersonFields = keyof Person
```

## Union type from object keys (`keyof typeof`)

```ts
const bob = {
    first_name: 'Bob',
    last_name: 'Jones',
    age: 27
}

// 'first_name' | 'last_name' | 'age'
type PersonFields = keyof typeof bob
```

## Union type from object values

```ts
const bob = {
    first_name: 'Bob',
    last_name: 'Jones',
    age: 27
}

// string | number
type PersonFields = (typeof bob)[keyof typeof bob]
```

- To make it more specific, mark the object with `as const`

```ts
const bob = {
    first_name: 'Bob',
    last_name: 'Jones',
    age: 27
} as const

// 'Bob' | 'Jones' | 27
type PersonFields = (typeof bob)[keyof typeof bob]
```
## Type with keys from object keys

```ts
const StorageKeys = {
    name: 'user_name',
    email: 'user_email',
    phone: 'user_phone',
} as const

type StorageKey = {
    -readonly [key in keyof typeof StorageKeys]: string
}
```

## Type from array items

```ts
const people = [{ name: 'Bob', age: 23 }, { name: 'Susan', age: 16 }]
type Person = typeof people[number] // { name: string, age: number }

const animals = ['cat', 'dog', 'mouse'] as const

// 'cat' | 'dog' | 'mouse'
type Animal = typeof animals[number]
// 'dog'
type Dog = typeof animals[1]
```

## Union type from property values in array of objects

```ts
const animals = [
  { species: 'cat', name: 'Fluffy' },
  { species: 'dog', name: 'Fido' },
  { species: 'mouse', name: 'Trevor' }
] as const

// 'cat' | 'dog' | 'mouse'
type Animal = typeof animals[number]['species']
```

## Index signature from literal type union

```ts
type ColorChannel = 'red' | 'green' | 'blue' | 'alpha'

interface Color {
    /* this won't work */
    // [channel: ColorChannel]: number

    /* use this instead */
    [channel in ColorChannel]: number
}
```

## Pick properties of `T`, omitting properties of `BaseModel`

```ts
type ModelFields<T> = Omit<T, keyof BaseModel>
```

## Pick all properties of type `Value` from `T`

```ts
type PickByType<T, Value> = {
  [P in keyof T as T[P] extends Value | undefined ? P : never]: T[P]
}
```

# Configuration

- `strict`: enables much stricter type checking

## TypeScript ESLint

- `no-floating-promises`: warns if you forget to await an async function
- `no-misused-promises`: warns if you use Promises in locations that don't make sense

# See also

- [[Development/Cheat sheets/JavaScript cheat sheet\|JavaScript cheat sheet]]
- [[Development/Clipped/TypeScript Utility Types\|TypeScript Utility Types]]
- [[Development/Clipped/Use TypeScript Mapped Types Like a Pro\|Use TypeScript Mapped Types Like a Pro]]
