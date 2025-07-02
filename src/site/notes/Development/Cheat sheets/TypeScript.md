---
{"dg-publish":true,"dg-path":"Cheat sheets/TypeScript.md","permalink":"/cheat-sheets/type-script/","tags":["language/typescript"]}
---


# Type Aliases vs. Interfaces

> [!tip]
> When creating types for objects, prefer interfaces when possible.

- Interfaces always describe objects, type aliases can also describe primitives and unions
- Interfaces can be added to after creation through [[#Interface declaration merging|declaration merging]], type aliases can't
- Interfaces can be more efficient to compile

# Tuples

- An array with a fixed number of elements, and a fixed type for each element position
- `string[]` creates an array which can hold any number of string values, `[string]` creates a tuple which holds a single string
    - `[string][]` is an array that holds any number of `[string]` tuples (ex. `[['a'], ['b']]`), `[string[]]` is a tuple which holds a single `string[]` array (ex. `[['a', 'b']]`)
- Tuples can mix types (ex. `[string, number]`)
- Tuple elements can be optional: `[number, number?, number?]` is a tuple that can hold between 1 and 3 numbers

```ts
type NameAndAge = [string, number]

const person: NameAndAge = ['Bob', 42]
const [name, age] = person // destructuring
```

- You can use a spread to indicate a set of known elements, followed by any number of extra elements

```ts
// an array with at least two numbers
type operands = [number, number, ...number[]]

// an array with a string, number, and any number of other arguments of any type
type arguments = [string, number, ...any[]]
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

- enums exist at runtime, and numeric enums have a reverse mapping between keys and values

```ts
enum Color { Red = 1, Blue }

console.log(Colors.Red) // 1
console.log(Colors[1]) // "Red"
```

- You can also create enums with string values
- String enums **do not** get a reverse mapping, but their values can be easier to understand at runtime

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
    C = A + B, // still constant because it's evaluated at compile time
    // this is computed
    Z = "aaaaa".length
}
```

## Const enums

- Enums with **only constant** members can be marked with `const` to boost performance - they will be removed during compilation and their values will be inlined

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

# Template literal types

```ts
type PlanType = 'individual' | 'family'
type PlanSchedule = 'monthly' | 'annual'

type PlanString = `${PlanType}.${PlanSchedule}`
// 'individual.monthly' | 'individual.annual' | 'family.monthly' | 'family.annual'

type PlanStringCamelCase = `${PlanType}${Capitalize<PlanSchedule>}`
// 'individualMonthly' | 'individualAnnual' | 'familyMonthly' | 'familyAnnual'
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

# Narrow type of `for...in` loop key

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

# Arrow functions with generics

- the comma hints that T is a generic and not a JSX tag
    - the comma isn't necessary if you're using `extends`

```ts
const arrowFunc = <T,>(value: T) => { /* ... */ }
```

# Function return type based on parameters

- might require TypeScript 5.8+

```ts
enum SelectionKind {
    Single,
    Multiple,
}

interface QuickPickReturn {
    [SelectionKind.Single]: string;
    [SelectionKind.Multiple]: string[];
}

async function showQuickPick<S extends SelectionKind>(
    prompt: string,
    selectionKind: S,
    items: readonly string[],
): Promise<QuickPickReturn[S]> {
    // returns String if SelectionKind is Single, and String[] if SelectionKind is Multiple
}
```

# Function overloads

- the *implementation signature* (the final one) is what's used in the function body, but is invisible to function callers

```typescript
interface Fruit { /* ... */ }
interface Apple extends Fruit { /* ... */ }

function getFruits(options?: { applesOnly: false }): Fruit[]
function getFruits(options: { applesOnly: true }): Apple[]
function getFruits({ applesOnly = false } = {}): Fruit[]  {
    /* function body here */
}

// type Fruit[]
const fruitSaladIngredients = getFruits()
// type Fruit[]
const tartIngredients = getFruits({ applesOnly: false })

// type Apple[] - even though the implementation signature declares a return type of Fruit[], TypeScript knows to narrow it based on the overload
const applesauceIngredients = getFruits({ applesOnly: true })
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

# `infer`

- Used when writing a conditional type, to infer a new type variable from part of the condition

```ts
type Flatten<Type> = Type extends Array<infer Item> ? Item : Type;
type Flat1 = Flatten<number[]> // number
type Flat2 = Flatten<string> // string

type GetReturnType<Type> = Type extends (...args: never[]) => infer Return
  ? Return
  : never;
type Num = GetReturnType<() => number>; // number
type Bools = GetReturnType<(a: boolean, b: boolean) => boolean[]>; // boolean[]
type NotFunction = GetReturnType<string> // never
```

# Type Manipulation

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

## Interface declaration merging

- Two interfaces with the same name will be merged automatically

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

## Combine index signatures and known properties

```ts
interface Foo {
  length: number;
}

interface Bar {
  [key: string]: string;
}

type FooBar = Foo | Bar;

const foo: FooBar = {
  length: 1, // OK
  txt: "TXT", // OK
  hello: 1 // not allowed
};
```

## Get type of a property (indexed access types)

```ts
interface Book {
    genre: 'comedy' | 'drama' | 'mystery'
}

interface Movie {
    genre: Book['genre']
}
```

## Remap properties from another type or union (mapped types)

- the result must be a type, not an interface

```ts
interface Features {
  darkMode: () => void;
  newUserProfile: () => void;
};

// { darkMode: boolean; newUserProfile: boolean; }
type FeatureOptions = {
  [Property in keyof Features]: boolean;
}
```

- also works with unions

```ts
type ColorChannel = 'red' | 'green' | 'blue' | 'alpha'

interface Color {
    /* this won't work */
    // [channel: ColorChannel]: number

    /* use this instead */
    [channel in ColorChannel]: number
}
```

- exclude keys by producing `never` (such as with the Exclude utility type)

```ts
type RemoveKindField<Type> = {
    [Property in keyof Type as Exclude<Property, "kind">]: Type[Property]
};
 
interface Circle {
    kind: "circle";
    radius: number;
}
 
type KindlessCircle = RemoveKindField<Circle>;
// { radius: number; }
```

- use `as` to remap keys

```ts
type EventConfig<Events extends { kind: string }> = {
    [E in Events as E["kind"]]: (event: E) => void;
}
 
type SquareEvent = { kind: "square", x: number, y: number };
type CircleEvent = { kind: "circle", radius: number };

// {
//   square: (event: SquareEvent) => void;
//   circle: (event: CircleEvent) => void;
// }
type Config = EventConfig<SquareEvent | CircleEvent>
```

- works with [[#Template Literal Types]] as well

```ts
type Getters<Type> = {
    [Property in keyof Type as `get${Capitalize<string & Property>}`]: () => Type[Property]
};
 
interface Person {
    name: string;
    age: number;
    location: string;
}

// {
//   getName: () => string;
//   getAge: () => number;
//   getLocation: () => string;
// }
type LazyPerson = Getters<Person>;
```

### Mapping modifiers

- You can add `readonly` or `?` modifiers, or remove them using `-readonly` or `-?`

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

## Union type from type/interface keys (`keyof`)

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

## Union type from array items

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

# Declaration files (`.d.ts`)

## Import types

- Declaration files with no `import` or `export` apply globally. Using a standard `import` at the top of the file will cause TypeScript to parse it as a normal module, meaning it must be imported to be used. Instead, use dynamic `import()`.

```ts
declare class Holiday {
  name: string
  date: import('dayjs').Dayjs
}
```

## Augment `window`

```ts
interface Window {
    globalProperty: string
}
```

## Extend globals (such as `process.env`)

```ts
declare global {
	namespace NodeJS {
		interface ProcessEnv {
			NODE_ENV: 'development' | 'sandbox' | 'production'
		}
	}
}
```

# Configuration

- `strict`: enables much stricter type checking

## TypeScript ESLint

- `no-floating-promises`: warns if you forget to await an async function
- `no-misused-promises`: warns if you use Promises in locations that don't make sense

# See also

- [[Development/Cheat sheets/JavaScript\|JavaScript]]
