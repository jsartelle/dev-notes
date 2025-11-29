---
{"dg-publish":true,"dg-path":"Cheat sheets/React.md","permalink":"/cheat-sheets/react/","tags":["language/react"]}
---


# JSX

- may need to `import React` in order to use JSX (not required in [[Development/Cheat sheets/Next.js\|Next.js]])
- expressions in JSX use **single curly braces**

```jsx
<h1>Hello, {user.name}</h1>
```

- wrap multi-line JSX in parentheses to avoid semicolon issues
- all tags must be closed, and can be self-closing
- literal less than and greater than symbols must be written with HTML codes (`&lt;`/`&gt;`)

## Attributes & Props

- attributes and props use quotes for string values or curly braces for expressions
    - React prop names are camelCased, HTML attributes are kebab-cased

```jsx
<img tabIndex="0" src={user.avatarUrl}></img>
```

- you can spread an object of props using `...`

```jsx
const selectedPerson = {
    name: "Bob",
    age: 23,
    job: "Developer"
}

<Person {...selectedPerson} />
```

## Classes and styles

- classes are added using `className`
- the `style` attribute accepts an object instead of a string
    - use double curly braces because it is an object inside a JSX expression
    - numeric values automatically have "px" appended where appropriate

```jsx
<img className="avatar" style={{ height: 10 }} />
```

## Conditional rendering

- JSX elements can be stored in variables

```jsx
const button = <LoginButton />;
return (
    <div>
        {button}
    </div>
)
```

- `&&` can be used in expressions to conditionally include elements

```jsx
return (
    <div>
        <h1>Hello!</h1>
        {unreadMessages.length > 0 &&
            <h2>
                You have {unreadMessages.length} unread messages.
            </h2>
        }
    </div>
)
```

- can also use ternary conditional operator

```jsx
return (
    <div>
        {isLoggedIn
            ? <LogoutButton />
            : <LoginButton />
        }
    </div>
)
```

- to avoid rendering a component, **return null** from its `render` function
    - class component lifecycle methods like `componentDidUpdate` will still be called

```jsx
function WarningBanner(props) {
    {/* will not be rendered if props.warn is falsy */}
    if (!props.warn) {
        return null;
    }

    return (
        <div className="warning">
        Warning!
        </div>
    );
}
```

- Use the `hidden` HTML attribute to include elements in the DOM, but not render them (similar to v-show in Vue)

## Lists & keys

- render arrays using a map function to create the corresponding elements
- list items should have a unique (among siblings) **string** key
    - do not use index as key if item order may change
    - key is **not a prop**, and is not passed to the component as one

```jsx
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map(number => 
    <li key={number.toString()}>
        {number}
    </li>
);

ReactDOM.render(
    <ul>{listItems}<ul>,
    document.getElementById('root')
);
```

- can inline the `map()` call, but be mindful of readability

```jsx
const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
    <ul>
        {numbers.map(number => 
            <ListItem key={number.toString()} value={number} />
        )}
    </ul>
);
```

### Force a component to re-mount

- you can force a component to re-mount by changing its key, even if it's not in a list
- this should be a last resort - for example, if you use a third-party component that doesn't re-render correctly when a prop changes

# Components

- component names **must start with a capital letter**
- the return value is what is rendered
- to render multiple elements wrap them in a fragment:

```jsx
function Cafe() {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};
```

- can also render multiple elements by returning an array with keys on each element (see [[#Lists Keys|Lists & Keys]])

## Component purity

Component rendering should be **pure**, meaning the same inputs will produce the same output. Any data the component depends on should be stored as [[#Props|props]] or [[#useState|state]].

You can wrap your app in `<React.StrictMode>` to call each component twice during development to find impure components.

## Controlled vs. uncontrolled components

Components that are primarily driven through [[#Props|props]] are sometimes referred to as *controlled components*, because their parent controls their behavior. Components that keep their primary information in local state are called *uncontrolled components*.

Example of a controlled `<input>`:

```jsx
const [firstName, setFirstName] = useState('')

return <input value={firstName} onChange={e => setFirstName(e.target.value)} />
```

## Server Components vs. Client Components

- Server Components:
    - are available in React 19, and Next.js when using the [[Development/Cheat sheets/Next.js#App Router\|App Router]]
    - are rendered on the server, and only the HTML is sent to the client (no hydration)
    - cannot use [[#Hooks]], event listeners, or other interactive features
    - can be async and use `await` (which causes them to trigger [[#Suspense]])
    - can use Node APIs and access sensitive data like secrets, since only the final HTML is sent to the client
- Client Components:
    - are marked with `'use client'` at the top of the file
    - **can still be server-side rendered** for performance, but will be hydrated on the client to become interactive
- if you need to use library components that use client-only features, but aren't marked with `'use client'`, you can create wrapper components for them

```jsx
'use client'

import { Button } from 'ui-library'
export default Button
```

- Client Components create a *server-client boundary* - anything **imported** into a Client Component, including Server Components, will run on the client
    - Server Components that are passed into Client Components as props or children will not be rendered on the client
- to enforce that Server Components or helper functions **only** run on the server, install and import the `server-only` NPM package
    - you can import secrets into a helper file and mark that with `server-only`, to prevent accidentally importing them on the client

### cache

- only available in Server Components
- takes a function and returns a new function that memoizes results - if the returned function is called with the same arguments (by reference), the cached result is returned
    - errors are also cached
- each call to `cache` returns a new function with its own cache, even if you call `cache` with the same function twice
    - this means `cache` must be called outside components
    - you can export the memoized function from a module to let components share it
- only lasts for the duration of a single server request (to avoid leaking data between users) - meant for sharing data between components during a single page load, not for long-lived caching like [[Development/Cheat sheets/React Query\|React Query]]
- you can call the memoized function outside of a component, but it will only read or update the cache when used in a component

```jsx
import { cache } from 'react';
const getMetrics = cache(calculateMetrics);

getMetrics(user) // will calculate and cache the result
getMetrics(user) // will return the cached result without calling calculateMetrics again
```

- you can call the memoized function in a high level component to preload data fetched in a lower level component (in the example, Profile) before that component starts rendering

```jsx
const getUser = cache(async (id) => {
  return await db.user.query(id);
});

function Page({id}) {
  // start fetching the user data, even though the result isn't used here
  getUser(id);
  // ... some computational work
  return (
    <>
      <Profile id={id} />
    </>
  );
}

async function Profile({id}) {
  // if the getUser call from Page finished, will use the cache
  const user = await getUser(id);
  return (
    <section>
      <img src={user.profilePic} />
      <h2>{user.name}</h2>
    </section>
  );
}

async function Profile({id}) {
  // if the getUser call from Page finished, will use the cache
  const user = await getUser(id);
  return (
    <section>
      <img src={user.profilePic} />
      <h2>{user.name}</h2>
    </section>
  );
}
```

## memo (memoize components)

- wrap a component function in `memo` to avoid re-rendering the component when its parent re-renders, as long as its props are the same
    - referred to as a *memoized component*

```jsx
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>
})
```

- optional second argument is a function that takes the old and new props and returns `true` if the cached component can be used (the props have **not** changed)
    - usually not needed, the default is to compare the props objects with [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) (shallow equality)
- to best take advantage of `memo`, instead of passing objects as props, pass individual primitives or memoized [[#useMemo|objects]] & [[#useCallback|functions]]
    - if your component only cares if a value exists or not, instead of passing the value as a prop (which will trigger re-render each time the value changes), pass a boolean that indicates if the value exists
- like [[#useMemo]], should only be used when necessary

```jsx
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null
  return <CallToAction hasGroups={hasGroups} />
}
```

# Props

- props are passed the same way as [[#Attributes & Props|attributes]]
- props are **read-only**
- passed to function components as the first argument
    - can use destructuring to access them easier
- state which is shared by multiple components should be kept in their closest common ancestor, and passed down as props

```jsx
function Welcome({ firstName, lastName }) {
  return <h1>Hello, {firstName} {lastName}!</h1>;
}

const firstName = "Sara"
const element = <Welcome firstName={firstName} lastName="Smith" />;
```

## children (slots)

- components receive child elements in a prop named `children`
    - equivalent to [[Development/Cheat sheets/Vue#Slots\|Vue slots]]

```jsx
// inside Card.js
function Card({ children }) {
    return (
        <div className="card">
            {children}
        </div>
    )
}

// inside another component
<Card>
    <Avatar />
</Card>

<Card>
    <p>Hello world!</p>
</Card>
```

## Pass a component as a prop

```tsx
import type { ComponentType } from 'react'
import type { LucideProps } from 'lucide-react'

export interface CardProps {
    // Icon must be a component that accepts LucideProps
    // capitalized name tells React to treat it as a component
    Icon?: ComponentType<LucideProps>
}

export default function Card({ Icon }: CardProps) {
    return (
        <div>
            {Icon && <Icon size={50} />}
            <!-- other stuff -->
        </div>
    )
}

// usage
import { Database } from 'lucide-react'

<Card Icon={Database} />
```

## Render a tag name from a prop

```tsx
export interface ComponentProps {
    // Tag must be a valid HTML tag name
    // capitalized name tells React to treat it as a component
    Tag?: keyof JSX.IntrinsicElements
}

export default function Component({ Tag = 'div' }: ComponentProps) {
    return <Tag>Hello!</Tag>
}

// usage

<Component /> // renders <div>Hello!</div>
<Component Tag="article" /> // renders <article>Hello!</article>
```

# Events

- DOM event names are written in camelCase (ex. `onClick`)
- handlers for custom events are passed as props and called by the child component
    - custom event handler props are usually named `onSomething`, and the handler function is named `handleSomething`

```jsx
function MyButton() {
    function handleFireLasers() {
        alert('Lasers activated!')
    }

    return (
        <button onFireLasers={handleFireLasers}>
            Activate Lasers
        </button>
    )
}
```

- to pass arguments to event handlers, either of the below will work (extra arguments to `bind` are prepended)

```jsx
render() {
    return (
        <button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
        <button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
    )
}
```

# Actions

- you can pass a function (known as an [[#Actions|Action]]) to a `<form>` element's `action` prop to run that function when the form is submitted
- the function will receive a `FormData` object as its argument
- you can set the `formAction` prop on a `<button>` or `<input type="submit">` to override the form's `action` for just that button

```jsx
async function createUser(formData) {
    await fetch('/user', {
        method: 'POST',
        body: {
            name: formData.get('name'),
            email: formData.get('email'),
        }  
    })
}

<form action={createUser}>
    <input name="name" placeholder="name" />
    <input name="email" placeholder="email" />
    <button type="submit">Submit</button>
</form>
```

- you can pass additional arguments using `.bind`, or by adding `<input type="hidden">` elements to the form

```jsx
const [userId, setUserId] = useState(123)

async function createUser(userId, formData) { /* ... */ }

const createUserWithId = createUser.bind(null, userId)
```

- to use the return value from a form submission, see [[#useActionState/useFormState]]

## Server Actions

- action functions which run on the server that you can call from Client Components
    - [can also be used outside of forms with `startTransition`](https://react.dev/reference/rsc/use-server#calling-a-server-action-outside-of-form)
- Server Actions must be async and marked with `'use server'` at the top
    - or add `'use server'` to the top of a file to mark every function within as a Server Action
- can return any serializable value

```js
export async function createUser(formData) {
    'use server'
    await db.createUser({
        name: formData.get('name'),
        email: formData.get('email'),
    })
}
```

```jsx
import { createUser } from '@/util/actions'

export default function UserForm() {
    return (
        <form action={createUser}>
            <input name="name" placeholder="Name" />
            <input name="email" placeholder="Email" />
            <button type="submit">Submit</button>
        </form>
    )
}
```

# Hooks

- hooks let you:
    - avoid writing classes
    - reuse stateful logic (ex. connecting to a store) without adding more components
    - group related logic in different lifecycle methods together
- hooks re-run on every render, but many of them allow for persisting values between renders
- hooks must be imported from `react`
- hooks only run client-side - if using a framework that supports [[#Server Components vs. Client Components|Server Components]], components that use hooks must be marked with `'use client'`

## Rules of Hooks

- **only call Hooks at the top level** - hooks need to be called in the same order every time
    - if you want to call a hook from a condition or loop, extract a new component and put it there
- only call Hooks from components or custom Hooks, not standard JS functions

## useState

- use `useState` to preserve values between renders
    - the argument to `useState` is the initial state - can be a primitive or object

```jsx
function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

- if you call a function to get the initial state, it will be called on every render even though the result is only used the first time
    - if the function call is expensive, pass an *initializer* function which takes no arguments and returns the initial state

```jsx
// bad - createTodosExpensive is called on every render, but only used on the first render
const [todos, setTodos] = useState(createTodosExpensive(todoData))

// good - initializeTodos is called only once
const initializeTodos = () => createTodosExpensive(todoData)
const [todos, setTodos] = useState(initializeTodos)
```

- state should be treated as immutable - to update state objects or arrays, copy it to a new object/array and pass it to the `set` function
    - the `set` function replaces the entire state, no merging
- **state changes don't take effect until the next render**
    - if you need to update the same state multiple times in one render, pass a function to the setter

```jsx
const [number, setNumber] = useState(0);

// incorrect
setNumber(number + 1) // 0 + 1
setNumber(number + 1) // 0 + 1
setNumber(number + 1) // 0 + 1

// the final value of `number` will be 1
```

```jsx
const [number, setNumber] = useState(0);

// correct
setNumber(number => number + 1) // 0 + 1
setNumber(number => number + 1) // 1 + 1
setNumber(number => number + 1) // 2 + 1

// the final value of `number` will be 3
```

- to update the state from a child component, pass down the `set` function as a prop
- if the same type of component is rendered in the same tree position, it will keep its state
    - to reset the state without changing the position, change the [[#Lists & Keys|key]]

```js
// contrived example - Counter will keep its state when isFancy changes
return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
    </div>
)
```

## useEffect

- lets you perform side effects from a function component
    - primarily used for **synchronizing with external systems** - data fetching, setting up a subscription, etc.
- **You don’t need Effects to transform data for rendering** - transform the data at the top level instead (wrap the transformation in [[#useMemo]] if it's expensive)
- **You don’t need Effects to handle user events** - use [[#Events|event handlers]]
- since hooks only run client-side, you can wrap code that uses browser APIs like `localStorage` in an effect to use it in server-side rendered components
- can return a "cleanup" function that will run before each time the effect function runs, as well as on component unmount
- to limit how often the effect runs, pass an array of reactive values as the second argument - the effect (including cleanup) will only run if any of the array items have changed
    - if you pass an **empty array**, the effect will **only run once** on mount (and cleanup on unmount)
        - code should still be resilient to effects running multiple times, both for Fast Refresh and future-proofing
    - if there is **no dependency array**, the provided function runs on **every render** (including the first one), after React has updated the DOM
    - non-primitive dependencies should either be declared outside the render loop, or with [[#useMemo]] or [[#useCallback]] to avoid infinite loops

```jsx
const [port, setPort] = useState(3000);

// when port changes, the component will disconnect from the old port and connect to the new one
useEffect(() => {
    connectToPort(3000)
    return () => { disconnectFromPort(3000) };
}, [port])
```

### useLayoutEffect

- like `useEffect` but runs before the DOM updates
- useful for things like tooltips that need to measure an element to size themselves
- can cause performance issues because it blocks repainting
- doesn't run on the server (since there's no layout information)

## useMemo

- cache a calculation between renders (like [[Development/Cheat sheets/Vue 3#Computed Properties\|computed properties]] in Vue)
    - referred to as *memoization*
    - if the dependencies haven't changed, the cached value is returned
    - dependencies are provided the same way as [[#useEffect]]
- **only memoize when necessary** - memoization is only valuable if the calculation is expensive and the dependencies rarely change

```jsx
const sortTodos = useMemo(
    () => todos.sort(sortFunction),
    [todos, sortFunction]
)
```

### Log what changed to trigger useMemo

- Add this code before the line with useMemo:

```js
const [lastDeps, setLastDeps] = useState()
```

- And at the top of the useMemo callback:

```js
const deps = {
  /* copy the list of dependencies here (without []) */
}

if (lastDeps) console.debug('changed:', Object.keys(deps).filter(key => JSON.stringify(deps[key]) !== lastDeps[key]))

setLastDeps(Object.keys(deps).reduce((obj, key) => { obj[key] = JSON.stringify(deps[key]); return obj }, {}))
```

## useCallback

- cache a function definition so it only changes when one of its dependencies changes
    - the same as returning a function from [[#useMemo]]
- callbacks can also be declared outside the render function if they don't need access to component data or Hooks
- like [[#useMemo]], should only be used when necessary - may be useful when:
    - the function is passed to a [[#memo (memoize components)|memoized]] component
    - the function is used as a dependency of another hook

```jsx
const handleSubmit = useCallback((orderDetails) => {
    // ... function body here
}, [productId, referrer])
```

## useRef

- `useRef` will hold onto information between renders, but aren't tracked by React and **don't trigger re-render** when updated
- `useRef` should be used for information that is **not needed for rendering**, for example:
    - timeout and interval IDs
    - [[#DOM element refs|DOM elements]]

```js
const ref = useRef(0) // can be any value, like useState

// ref looks like this
{
    current: 0
}

useEffect(() => {
    // ref.current is mutable, but should only be read and written
    // in effects or handlers
    ref.current = ref.current + 1
})
```

- like [[#useState]], the initial value passed into `useRef` is only stored once but evaluated on every render, so avoid calling expensive functions
- **don't read or write `ref.current` during render (including in the JSX)**
    - reading or writing from [[#Events|event handlers]] or [[#useEffect]] is okay
    - if you need a value during render, use [[#useState]] instead
    - an exception is initializing `ref.current` only on the first render, for example when calling an expensive function

```jsx
// instead of this
const ref = useRef(new ExpensiveClass())

// do this
const ref = useRef(null)
if (playerRef.current === null) {
    playerRef.current = new ExpensiveClass()
}
```

### Accessing state or callbacks from timeouts

- if you access state from within a timeout callback, when the timeout fires it will show the state value at the moment the timeout was created, not when it fired
    - this also applies to functions and other values declared in a component's render function

```js
function MyComponent() {
    const [counter, setCounter] = useState(0);

    const eventHandler = () => {
        setCounter(counter + 1);
        // this will log the value of counter at the time
        // eventHandler was called, not when the timeout fires
        setTimeout(() => console.log(counter), 100);
    }

    return <button onClick={eventHandler}>{counter}</button>
}
```

- store the state value in a ref to avoid this (or replace the state with a ref entirely if you don't need it for rendering)

```js
function MyComponent() {
    const [counter, setCounter] = useState(0);
    const counterRef = useRef();

    counterRef.current = counter;

    const eventHandler = () => {
        setCounter(counter + 1);
        // this will log the updated value of counter
        setTimeout(() => console.log(counterRef.current), 100);
    }

    return <button onClick={eventHandler}>{counter}</button>
}
```

### Higher-order functions (Lodash debounce/throttle in React)

- instead of using `useCallback` with higher-order functions like lodash debounce, use a ref so the inner function isn't recreated

```js
// incorrect
useCallback(_.debounce(myFunction, 100), [myFunction])
// correct
useRef(_.debounce(myFunction, 100))
```

### DOM element refs

- to get a DOM element ref, set the initial value to `null`, then set the ref as the `ref` attribute on an element
- example uses:
    - focusing text inputs
    - scrolling to elements
- **avoid modifying the DOM manually!**

```jsx
const myRef = useRef(null)

<div ref={myRef}>
```

### Function refs (and refs for multiple DOM elements)

- you can also set the `ref` attribute to a function - this lets you do things like store multiple elements in a Map
- the function receives the node when setting the ref, and `null` when it should be cleared

```jsx
const rows = useRef(null)
function getRowMap() {
    if (!rows.current) {
        // initialize the map only once
        rows.current = new Map()
    }
    return rows.current
}

return (
    <tbody>
        {data.map((row) => (
            <tr key={row.id} ref={(node) => {
                const map = getRowMap()
                node ? map.set(row.id, node) : map.delete(index)
            }}></tr>
        ))}
    </tbody>
)
```

- in React 19, you can return a cleanup function from the ref function instead, like with [[#useEffect]]

```jsx
return (
    <tbody>
        {data.map((row) => (
            <tr key={row.id} ref={(node) => {
                const map = getRowMap()
                map.set(row.id, node)
                /* React 19+ */
                return () => map.delete(row.id)
            }}></tr>
        ))}
    </tbody>
)
```

### forwardRef (component refs)

- in React 19, refs are passed to function components as props, just like children

```jsx
const MyInput = ({ ref, ...props }) => {
    return <input {...props} ref={ref} />
}

// inside another component
<MyInput ref={inputRef} />
<button onClick={() => inputRef.current?.focus()}>Focus Input</button>
```

- in earlier versions, `ForwardRef` lets function components forward their ref to a child component or element
    - this makes it harder to refactor your component in the future (since users of your component may rely on behavior of the element the ref is forwarded to), so typically used for low-level components like custom buttons or inputs

```jsx
const MyInput = forwardRef((props, ref) => {
    return <input {...props} ref={ref} />
})

// inside another component
<MyInput ref={inputRef} />
<button onClick={() => inputRef.current?.focus()}>Focus Input</button>
```

#### useImperativeHandle

- lets you expose only certain values or functions through [[#forwardRef]]
- prefer props when possible - ex. instead of exposing `open` and `close` methods for a modal, add an `isOpen` prop

```jsx
const MyInput = forwardRef(function MyInput(props, ref) {
  const inputRef = useRef(null);

  function getValue() {
    return inputRef.current?.value;
  }

  useImperativeHandle(ref, () => {
    return {
      focus() {
        inputRef.current.focus();
      },
      getValue
    };
  }, []);

  return <input {...props} ref={inputRef} />;
});
```

#### use forwarded ref inside component

- to forward a ref and also use it inside the component, create an inner ref inside the component and use [[#useImperativeHandle]] to sync it with the forwarded ref

```tsx
export default forwardRef(function MyComponent(
    {}: MyComponentProps, 
    refForwarded: ForwardedRef<HTMLDivElement>
) {
    const innerRef = useRef<HTMLDivElement>(null);
    useImperativeHandle(refForwarded, () => innerRef.current!);

    return (
        <div ref={innerRef}>...</div>
    )
})
```

### Cleanup

- starting in React 19, ref functions can return a cleanup function (similar to [[#useEffect]]) that is called when the component unmounts
    - example: create a ResizeObserver to observe the DOM node, and clean it up on unmount

```jsx
<input ref={(ref) => {
    // ref creation logic here
    return () => {
        // cleanup here
    }
}}
```

## useId

- generate random IDs for use with form elements

```jsx
import { useId } from 'react'

export default function checkboxWithLabel() {
    const id = useId()

    return (
        <input type="checkbox" id={id}></input>
        <label htmlFor={id}>Checkbox</label>
    )
}
```

## useFormStatus

- lets you get the pending status of a form that is the parent of the current component (not within the current component!)
- useful if there are multiple forms on the page
- returns an object with these properties:
    - `pending`: the pending state of the form submission
    - `data`: the FormData
    - `method`: 'GET' or 'POST'
    - `action`: the `action` function of the parent form (if there is one)

```jsx
import { useFormStatus } from 'react-dom'

export default function SubmitButton() {
    const { pending } = useFormStatus()

    return (
        <button type="submit" disabled={pending}>
            {pending ? 'Please wait...' : 'Submit'}
        </button>
    )
}
```

## useActionState/useFormState

- lets you access the status and return value of an [[#Actions|Action]] used with a form (including Server Actions)
    - in some earlier canary releases (used by Next.js), it was known as `useFormState` and imported from `react-dom`
- the first argument is the Action function, the second argument is your initial state
    - the Action receives the state (see below) as its first argument, and the FormState as its second argument
- returns an array with three values: a state object, a wrapped version of the Action, and a `pending` flag
    - before the wrapped Action is called, the state is equal to the initial state you passed
    - after the wrapped Action is called at least once, the state is equal to the last return value of the Action
    - `useFormState` does not have the `pending` flag

```js
/* actions.js */
export async function createUser(currentState, formData) {
    'use server'
    const user = await db.createUser({
        name: formData.get('name'),
        email: formData.get('email'),
    })
    return user
}
```

```jsx
import { useActionState } from 'react'
import { createUser } from '@/util/actions'

export default function UserForm() {
    const [user, createUserForm, pending] = useActionState(createUser, null)

    return (
        <h2>Sign Up</h2>
        <form action={createUserForm}>
            <input name="name" placeholder="Name" />
            <input name="email" placeholder="Email" />
            <button type="submit" disabled={pending}>
                {pending ? 'Please wait...' : 'Submit'}
            </button>
        </form>

        { user ? (
            <h2>Your Profile</h2>
            <UserInfo user={user} />
        ) : null}
    )
}
```

## useReducer

- reducers let you consolidate state update logic from multiple event handlers into one function
    - because reducers take the state as an argument, you can declare them outside of the component
- similar to a Vuex store for a single piece of state
- use reducers to simplify complex components that update the same piece of state in a number of different way
    - for simpler components [[#useState]] tends to be easier to understand
- reducers must be [[#Component purity|pure]]
- each action describes a single **user interaction** - ex. if a user presses Reset on a form with five fields, dispatch one `reset_form` action instead of 5 separate `set_field` actions

```js
function tasksReducer(tasks, action) {
    // return next state
    switch (action.type) {
        case 'added':
            return [
                // tasks.push won't work because you can't mutate state
                ...tasks,
                {
                    id: action.id,
                    text: action.text
                }
            ]
        case 'deleted':
            return tasks.filter(t => t.id !== action.id)
    }
}
```

- to use the reducer in a component:

```js
import { useReducer } from 'react'

// instead of
// const [tasks, setTasks] = useState(initialTasks)
const [tasks, dispatch] = useReducer(tasksReducer, initialTasks)

function handleAddTask(text) {
  dispatch({
    type: 'added',
    id: nextId++,
    text: text,
  });
}

function handleDeleteTask(taskId) {
  dispatch({
    type: 'deleted',
    id: taskId,
  });
}
```

## useOptimistic

- show the "after" state of an async [[#Actions|Action]] optimistically while the request is happening
    - for example, if a user updates the name on their account, show the new name instantly while updating it on the server
- the value passed to `useOptimistic` (the current state) will be returned **unless** an async Action is pending, in which case the optimistic state (the value passed to the `setWhatever` function) is returned
    - either success or failure will cause the current state to be displayed again, so make sure to update that before the Action finishes

```jsx
import { useOptimistic } from 'react'

const updateNameAction = async (formData) => {
    const newName = formData.get('name')
    // start showing the optimistic state
    setOptimisticName(newName)
    const updatedName = await updateNameInDatabase(newName)
    // if updateNameInDatabase was successful,
    // update the current state
    onUpdateName(updatedName)
}

export default function changeName({ currentName, onUpdateName }) {
    const [optimisticName, setOptimisticName] = useOptimistic(currentName)

    return (
        <form action={updateNameAction}>
            <p>Your name is: {optimisticName}</p>
            <label>
                <span>Change Name:</span>
                <input name="name" disabled={currentName !== optimisticName} />
            </label>
        </form>
    )
}
```

## Custom Hooks

- are normal functions with any signature, but must start with `use` and follow the [[#Rules of Hooks]]
- only functions that **call** Hooks need to **be** Hooks - otherwise you can use regular functions

```jsx
import { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    /* ... */
  });

  return isOnline;
}

function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);
}
```

### useDebugValue

- lets you show a label for your custom Hook in React Devtools

```jsx
useDebugValue(isOnline ? 'Online' : 'Offline');
```

# Suspense

- lets you show fallback content while async child components load
    - this applies to child components using [[#use]], async [[#Server Components vs. Client Components|Server Components]], or [[#lazy]] loaded components
- `fallback` can be any JSX, including components or text
- the fallback is shown until all async children (at any depth) finish loading

```jsx
import { Suspense } from 'react';

{/* LoadingSpinner will be shown until both async components finish loading */}
<Suspense fallback={<LoadingSpinner />}>
    <AsyncComponentOne />
    <AsyncComponentTwo />
</Suspense>
```

## lazy

- lets you lazy load a component's code
    - pass it a function which returns a Promise, that resolves to an object whose `.default` property is a React component
    - returns a component that will Suspend until the component is loaded

```jsx
import { lazy } from 'react'

const MarkdownPreview = lazy(() => import('./MarkdownPreview.js'))

/* the result is an async component */
<Suspense fallback="Loading...">
    <MarkdownPreview />
</Suspense>
```

## startTransition and useTransition

- `startTransition` lets you mark code that would trigger a component to suspend as not urgent, so the previous state is shown until the update finishes

```jsx
import { useState, startTransition } from 'react'

function PageView() {
    const [page, setPage] = useState(0)

    function switchPage() {
        startTransition(() => setPage(page === 0 ? 1 : 0))
    }

    // assume FirstPage and SecondPage are async components
    return (
        <div>
            <Button onClick={switchPage}>Switch Page</button>
            page === 0 ? <FirstPage /> : <SecondPage />
        </div>
    )
}

```

- `useTransition` returns a copy of `startTransition`, as well as an `isPending` flag so you can update your UI while the transition is happening

```jsx
import { useState, useTransition } from 'react'

function PageView() {
    const [page, setPage] = useState(0)
    const [isPending, startTransition] = useTransition()

    function switchPage() {
        startTransition(() => setPage(page === 0 ? 1 : 0))
    }

    // assume FirstPage and SecondPage are async components
    return (
        <div>
            <Button disabled={isPending} onClick={switchPage}>
                {isPending ? 'Loading...' : 'Switch Page'}
            </button>
            page === 0 ? <FirstPage /> : <SecondPage />
        </div>
    )
}
```

## useDeferredValue

- update a value asynchronously, but keep using the old value during the update instead of [[#Suspense|suspending]]
- in the example below, assume that SearchResults suspends while fetching the results for the given query
    - when the query is updated, the input will update automatically since it's using the non-deferred `query`
    - but since SearchResults is using `deferredQuery`, instead of suspending it will continue to display the results from the previous query until it finishes fetching

```jsx
import { Suspense, useState, useDeferredValue } from 'react';
import SearchResults from './SearchResults.js';

export default function App() {
  const [query, setQuery] = useState('');
  const deferredQuery = useDeferredValue(query);
  return (
    <>
      <label>
        Search albums:
        <input value={query} onChange={e => setQuery(e.target.value)} />
      </label>
      <Suspense fallback={<h2>Loading...</h2>}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </>
  );
}
```

## use

- available in React 19 and recent versions of Next.js
- **not a hook**, so it can be used within loops or conditionals
- lets you render a Client Component asynchronously, and [[#Suspense|suspend]] until the given promise resolves
    - Server Components don't need `use` since they can use `await` natively
- promises can be passed from [[#Server Components vs. Client Components|Server Components]] to Client Components as props and "awaited" with `use`

```jsx
'use client'
import { use } from 'react'

export default function UserInfo({ userPromise }) {
    const userData = use(userPromise)

    return (
        <div>
            <span>{user.name}</span>
            {/* etc. */}
        </div>
    )
}
```

```JSX
/* this is a Server Component */
export default function ProfilePage() {
    const userDataPromise = db.getUserById(123) // not awaited

    return (
        {/* promise is passed from server to client as a prop */}
        <Suspense fallback="Loading user data...">
            <UserInfo userPromise={userDataPromise} />
        </Suspense>
    )
}
```

- can also be used to read a [[#Context]]

# flushSync

- forces pending work to be flushed and the DOM to update synchronously
- like `tick` in Svelte or `nextTick` in Vue
- hurts performance, so use sparingly

```jsx
import { flushSync } from 'react-dom';

flushSync(() => {
  /* ... */
})
```

# Error Boundaries

- by default, any errors thrown inside a component take down the whole app
- to avoid this, you can create an *Error Boundary* - a special component that catches any errors inside of it, and can show an error message or other fallback instead
- error boundaries must be class components
    - [react-error-boundary](https://github.com/bvaughn/react-error-boundary) provides a premade Error Boundary component

# Context

- lets a component pass data down an arbitrary distance in the tree, without having to pass props through multiple levels of components that might not need them (prop drilling)
    - contexts can have performance issues at scale, because the component will re-render if any property in the context changes, even if the component doesn't use that property - store libraries like [[Development/Cheat sheets/MobX\|MobX]] or [[Development/Cheat sheets/Redux\|Redux]] can solve this
- create a file for the context, and call `createContext` with a default value
    - the default value is used if the component calling `useContext` doesn't have a context provider above it

```jsx
import { createContext } from 'react'

export const LevelContext = createContext(1)
```

- in the higher level component, import the context and wrap the children in a context provider
    - you can't read from a context in the same component that declares the provider

```jsx
import { LevelContext } from './LevelContext'

/* here the context value (`level`) is a prop on the higher level component, but it could come from state or anywhere */
export default function Section({ level, children }) {
    return (
        <section>
            {/* if anything inside the context provider calls `useContext(LevelContext)`, it will get the value of `level` */}
            <LevelContext.Provider value={level}>
                {children}
            </LevelContext.Provider>
        </section>
    )
}
```

- use the context in the child component

```jsx
import { useContext } from 'react'
import { LevelContext } from './LevelContext'

export default function Heading({ children }) {
    const level = useContext(LevelContext)

    return (/* ... */)
}
```

- you can override a context value by adding another context provider (using the same context) lower in the tree, just like shadowing variables

- in React 19, you can use the context itself as the provider rather than `Context.Provider`

```jsx
<LevelContext value={level}>
    {children}
</LevelContext>
```

- in React 19 you can read a context with [[#use]] - since it isn't a Hook, you can do this in conditionals or loops

```jsx
const level = use(LevelContext)
```

# Metadata (\＜head\＞ tags)

- starting in React 19, `<title>`, `<link>`, and `<meta>` tags inside components will be hoisted to the `<head>`

```jsx
export default function BlogPost({ post }) {
    return (
        <article>
            <title>{post.title}</title>
            <meta name="author" content={post.author} />
            <link rel="author" href={post.authorUrl} />
            ...
        </article>
    )
}
```

# TypeScript

- `PropsWithChildren` adds the `children` prop with correct typing

```tsx
import type { PropsWithChildren } from 'react'

interface PersonProps extends PropsWithChildren {
  name: string
  age?: number
}
function Person({ name, age, children }: PersonProps) {
  /* ... */
}

// PropsWithChildren can also accept a type argument
interface CardProps {
  title: string
}
function CardWithChildren = (
  { title, children }: PropsWithChildren<CardProps>
) {
  /* ... */
}
```

- use `ComponentProps` with `typeof` to get a component's prop type

```tsx
type PageProps = ComponentProps<typeof Page>
```

- use `HTMLProps` to extend HTML elements

```tsx
type ButtonProps extends HTMLProps<HTMLButtonElement> {
    primary?: boolean
}

function Button({ className, children, primary }: ButtonProps) {
    /* ... */
}
```

- type style attribute objects as `React.CSSProperties`

```tsx
const buttonStyle: React.CSSProperties = {
    backgroundColor: Colors.blue;
}

return <button style={buttonStyle}></button>
```

## Component and element types

- `JSX.Element` accepts only elements
- `ReactNode` accepts anything that React can render (elements, strings, null/undefined, etc.)
- `ComponentType<props>` accepts component constructors - use this if you get "*JSX element type '...' does not have any construct or call signatures*" errors

## Prop types with generics

```tsx
interface DataGridProps<T extends { id: any }> {
    /* data is an array of objects with an `id` property of any type */
    data: T[]
    selectedIds: Set<T>
    onSelect: (id: T['id']) => void
}

function DataGrid<T extends { id: any }>(props: DataGridProps<T>) {
    /* ... */
}
```

## forwardRef

- takes 2 type arguments, the ref type and the component props type

```tsx
const ButtonWrapper = forwardRef<HTMLButtonElement, ButtonProps>(
    { label, children }, // type ButtonProps
    ref // type ForwardedRef<HTMLButtonElement>
) {
    return <button ref={ref}>...</button>
}
```

### Type HOC using forwardRef

https://codesandbox.io/p/sandbox/sleepy-kilby-1zyof?file=%2Fsrc%2FwithStatusMessages.tsx

```tsx
function withStatusMessages<P extends object>(
  WrappedComponent: React.ComponentType<P>
): React.FunctionComponent<P & withStatusMessagesProps> {
  return ({ errorText, successText, ...props }) => {
    return (
      <>
        <WrappedComponent {...props as P} />
        {errorText ? <div className="errorText">{errorText}</div> : null}
        {successText ? <div className="successText">{successText}</div> : null}
      </>
    );
  };
}
```

# Creating a project

## create-react-app

> [!warning]
> create-react-app is deprecated and not as performant as other solutions. Try [[#Vite]] instead for a simple app, or [[Development/Cheat sheets/Next.js\|Next.js]] for a full-featured app with routing.

## Vite

- built-in support for TypeScript, Sass/Less/Stylus
- no built-in routing
- [create-vite-extra](https://github.com/bluwy/create-vite-extra) can be used to set up a project with SSR support

- create a basic app with TypeScript

```bash
npm create vite@latest my-app -- --template react-ts
```

- use SWC instead of Babel (faster, but less community support)

```bash
npm create vite@latest my-app -- --template react-swc-ts
```

## Next.js

[[Development/Cheat sheets/Next.js\|Next.js]]

# Libraries

## React Query/TanStack Query

[[Development/Cheat sheets/React Query\|React Query]]

## Flip Move

<div class="rich-link-card-container"><a class="rich-link-card" href="http://joshwcomeau.github.io/react-flip-move/examples/#/?_k=q79qet" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://s3.amazonaws.com/githubdocs/favicon.png?v=2')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">React Flip Move - Shuffle</h1>
		<p class="rich-link-card-description">

		</p>
		<p class="rich-link-href">
		http://joshwcomeau.github.io/react-flip-move/examples/#/?_k=q79qet
		</p>
	</div>

</a></div>

- Lets you easily animate changes to list items
- Make sure that children have a `key`, and function components are wrapped in [[#forwardRef (component refs)|forwardRef]]

```jsx
const Item = forwardRef((props, ref) => (
    <div ref={ref}>{props.name}</div>
))

<FlipMove>
    {listItems.map(item => {
        <Item key={item.id} {...item} />
    })}
</FlipMove>
```

# See also

- [[Development/Cheat sheets/React Native\|React Native]]
- [[Development/Cheat sheets/Next.js\|Next.js]]
- [[Development/Cheat sheets/MobX\|MobX]]
