---
{"dg-publish":true,"dg-path":"Cheat sheets/React cheat sheet.md","permalink":"/cheat-sheets/react-cheat-sheet/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://react.dev/learn" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://react.dev/images/og-learn.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Quick Start – React</h1>
		<p class="rich-link-card-description">
		The library for web and native user interfaces
		</p>
		<p class="rich-link-href">
		https://react.dev/learn
		</p>
	</div>
</a></div>

# JSX

- expressions in JSX use **single curly braces**

```jsx
<h1>Hello, {user.name}</h1>
```

- wrap multi-line JSX in parentheses to avoid semicolon issues
- all tags must be closed

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

## Classes and Styles

- classes are added using `className`
- the `style` attribute accepts an object instead of a string
    - use double curly braces because it is an object inside a JSX expression
    - numeric values automatically have "px" appended where appropriate

```jsx
<img className="avatar" style={ { height: 10 } } />
```

# Rendering

- to render an element into a root DOM node use `ReactDOM.render(element, rootNode)`
    - React apps normally have a single root node, and most only call `ReactDOM.render` once

# Components

- component names **must start with a capital letter**
- the return value is what is rendered
- to render multiple elements wrap them in fragments:

```jsx
const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};
```

- can also render multiple elements by returning an array with keys on each element (see [[Development/Cheat sheets/React cheat sheet#Lists Keys\|Lists & Keys]])

## Component purity

Component rendering should be **pure**, meaning the same inputs will produce the same output. Any data the component depends on should be stored as [[Development/Cheat sheets/React cheat sheet#Props\|props]] or [[Development/Cheat sheets/React cheat sheet#useState\|state]].

You can wrap your app in `<React.StrictMode>` to call each component twice during development to find impure components.

## Controlled vs. uncontrolled components

Components that are primarily driven through [[Development/Cheat sheets/React cheat sheet#Props\|props]] are sometimes referred to as *controlled components*, because their parent controls their behavior. Components that keep their primary information in local state are called *uncontrolled components*.

# Props

- props are passed the same way as [[Development/Cheat sheets/React cheat sheet#Attributes & Props\|attributes]]
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
    - equivalent to [[Development/Cheat sheets/Vue cheat sheet#Slots\|Vue slots]]

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

# Events

- DOM event names are written in camelCase
    - instead of custom events like Vue, React uses callbacks passed as props and called by the child component
- event handlers are passed as functions

```jsx
function MyButton() {
    function activateLasers() {
        alert('Lasers activated!')
    }

    return (
        <button onClick={activateLasers}>
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

# Conditional Rendering

- React elements can be stored in variables

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

# Lists & Keys

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

- can inline the `map()` call:

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

# Hooks

- Hooks let you:
    - avoid writing classes
    - reuse stateful logic (ex. connecting to a store) without adding more components
    - group related logic in different lifecycle methods together
- Hooks must be imported from React

## Rules of Hooks

- **only call Hooks at the top level** - hooks need to be called in the same order every time
    - if you want to call a hook from a condition or loop, extract a new component and put it there
- only call Hooks from React functions (components or custom Hooks), not standard JS functions

## Custom Hooks

- are normal functions with any signature, but must start with `use` and follow the [[Development/Cheat sheets/React cheat sheet#Rules of Hooks\|#Rules of Hooks]]
- can call other Hooks

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

# useState

- use `useState` to preserve variables between renders
- the argument to `useState` is the initial state
    - can be any kind of value, including objects
- state should be treated as read-only - to update state objects or arrays, copy it to a new object/array and pass it to the `set` function
    - the `set` function always replaces, never merges
- state changes are batched and ==don't take effect until the next render==
    - if you need to update the same state multiple times in each render, pass a function to the setter

```jsx
const [number, setNumber] = useState(0);

setNumber(number + 1) // 0 + 1
setNumber(number + 1) // 0 + 1
setNumber(number + 1) // 0 + 1

// the final value of `number` will be 1
```

```jsx
const [number, setNumber] = useState(0);

setNumber(number => number + 1) // 0 + 1
setNumber(number => number + 1) // 1 + 1
setNumber(number => number + 1) // 2 + 1

// the final value of `number` will be 3
```

- to update the state from a child component, pass down the `set` function as a prop

```jsx
function Example() {
  // Declare a new state variable, which we'll call "count"
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

- if the same type of component is rendered in the same tree position, it will keep its state
    - to reset the state without changing the position, change the [[Development/Cheat sheets/React cheat sheet#Lists & Keys\|key]]

```js
// Counter will keep its state when isFancy changes
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

# useEffect

- lets you perform side effects from a function component
    - primarily used for **synchronizing with external systems** - data fetching, setting up a subscription, etc.
    - **You don’t need Effects to transform data for rendering** - transform the data at the top level instead (use [[Development/Cheat sheets/React cheat sheet#useMemo\|#useMemo]] if the transformation is expensive)
    - **You don’t need Effects to handle user events** - use [[Development/Cheat sheets/React cheat sheet#Events\|event handlers]]
- can return a "cleanup" function that will run before each time the effect function runs, as well as on component unmount
- by default the provided function runs on every render, including the first one, ==after React has updated the DOM==
    - if you don't want it to run on the first render use [[Development/Cheat sheets/React cheat sheet#useLayoutEffect\|#useLayoutEffect]]
- to react to prop changes, pass an array of reactive values as the second argument to `useEffect` - the effect (including cleanup) will only run if any of the array items have changed
    - if you pass an empty array, the effect will only run once on mount (and cleanup on unmount)
        - code should still be resilient to effects running multiple times, both for Fast Refresh and future-proofing
- if you need to call a function inside the render loop in useEffect, define it using [[Development/Cheat sheets/React cheat sheet#useCallback\|#useCallback]]

```jsx
const [port, setPort] = useState(3000);

// if port changes, the component will disconnect from the old port and connect to the new one
useEffect(() => {
    connectToPort(3000)
    return () => { disconnectFromPort(3000) };
}, [port])
```

# useLayoutEffect

- like [[Development/Cheat sheets/React cheat sheet#useEffect\|#useEffect]] but fires before the DOM changes, blocking the browser from painting
    - won't run on the first render, but the result of the first render won't be displayed
    - use `useEffect` instead whenever possible

# useRef

- `useRef` will hold onto information between renders, but aren't tracked by React and **don't trigger re-render** when updated
- `useRef` should be used for information that is **not needed for rendering**, for example:
    - timeout and interval IDs
    - [[Development/Cheat sheets/React cheat sheet#DOM element refs\|DOM elements]]
- **don't read or write `ref.current` during render**, unless only doing it on the first render (by checking `ref.current`), otherwise your component will no longer be [[Development/Cheat sheets/React cheat sheet#Component purity\|pure]]
    - reading or writing from [[Development/Cheat sheets/React cheat sheet#Events\|event handlers]] or [[Development/Cheat sheets/React cheat sheet#useEffect\|#useEffect]] is okay

```js
const ref = useRef(0) // can be any value, like useState

// ref looks like this
{
    current: 0
}

useEffect(() => {
    // ref.current is mutable
    ref.current = ref.current + 1
})
```

- the initial value passed into `useRef` is only stored once, but evaluated on every render, so avoid calling expensive functions

```jsx
// instead of this
const ref = useRef(new ExpensiveClass())

// do this
const ref = useRef(null)
if (playerRef.current === null) {
    playerRef.current = new ExpensiveClass()
}
```

## DOM element refs

- to get a DOM element ref, set the initial value to `null`, then set the ref as the `ref` attribute on an element
- example uses:
    - focusing text inputs
    - scrolling to elements
- **avoid modifying the DOM manually!**

```jsx
const myRef = useRef(null)

<div ref={myRef}>
```

## forwardRef

- allows functional components to receive a ref and forward it to a child component or element
    - this makes it harder to refactor your component in the future (since users of your component may rely on behavior of the element the ref is forwarded to), so typically used for low-level components like custom buttons or inputs

```jsx
const MyInput = forwardRef((props, ref) => {
    return <input {...props} ref={ref} />
})

// inside another component
<MyInput ref={inputRef} />
<button onClick={() => inputRef.current?.focus()}>Focus Input</button>
```

# useMemo

- cache a calculation between renders (similar to [[Development/Cheat sheets/Vue 3 cheat sheet#Computed Properties\|computed properties]] in Vue)
    - referred to as *memoization*
    - if the dependencies haven't changed, the cached value is returned
    - dependencies are provided the same way as [[Development/Cheat sheets/React cheat sheet#useEffect\|#useEffect]]
- to keep code readable, **avoid using `useMemo` and `memo` unless necessary**

```jsx
const sortTodos = useMemo(
    () => todos.sort(sortFunction),
    [todos, sortFunction]
)
```

## memo

- wrap a component function in `memo` to avoid re-rendering the component when its parent re-renders, as long as its props are the same
    - referred to as a *memoized component*

```jsx
const Greeting = memo(function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>
})
```

- optional second argument is a function that takes the old and new props and returns `true` if the cached component can be used (the props have **not** changed)
    - usually not needed, the default is to compare the props objects with [`Object.is`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/is) (shallow equality)
- to best take advantage of `memo`, instead of passing objects as props, pass individual primitives or memoized [[Development/Cheat sheets/React cheat sheet#useMemo\|objects]] & [[Development/Cheat sheets/React cheat sheet#useCallback\|functions]]
    - if your component only cares if a value exists or not, instead of passing the value as a prop (which will trigger re-render each time the value changes), pass a boolean that indicates if the value exists

```jsx
function GroupsLanding({ person }) {
  const hasGroups = person.groups !== null
  return <CallToAction hasGroups={hasGroups} />
}
```

# useCallback

- cache a function definition so it only changes when one of its dependencies changes
    - like [[Development/Cheat sheets/React cheat sheet#useMemo\|#useMemo]] but for functions
    - dependencies are provided the same way as [[Development/Cheat sheets/React cheat sheet#useEffect\|#useEffect]]
- callbacks can also be declared outside the render function if they don't need access to component data

```jsx
const handleSubmit = useCallback((orderDetails) => {
    // ... function body here
}, [productId, referrer])
```

# useReducer

- reducers let you consolidate state update logic from multiple event handlers into one function
    - because reducers take the state as an argument, you can declare them outside of the component
- similar to a Vuex store for a single piece of state
- use reducers to simplify complex components that update the same piece of state in a number of different way
    - for simpler components [[Development/Cheat sheets/React cheat sheet#useState\|#useState]] tends to be easier to understand
- reducers must be [[Development/Cheat sheets/React cheat sheet#Component purity\|pure]]
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

# Context

#todo https://react.dev/learn/passing-data-deeply-with-context

# TypeScript

```tsx
import { FC, ReactElement } from 'react'

interface PersonProps {
  name: string
  age?: number
}

// the FC type automatically wraps the props in PropsWithChildren
const Person: FC<PersonProps> = ({ name, age, children }): ReactElement => {
  return (
    <div>
      <span>Hello {name}!</span>
      {age ? <span>You are {age} years old.</span> : null}
      {children}
    </div>
  )
}

```

- In practice, you usually just need to type the props
    - you can use `PropsWithChildren` to add the `children` prop

```tsx
export default function Person({ name, age, children }: PropsWithChildren<PersonProps>) {
    // ...
}
```

- use `ComponentProps` with `typeof` to get a component's prop type

```ts
type PageProps = ComponentProps<typeof Page>
```

# Libraries

## create-react-app

- create a new single-page React app using TypeScript:

```shell
npx create-react-app my-app --template typescript
```

## Styled Components

<div class="rich-link-card-container"><a class="rich-link-card" href="https://styled-components.com/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.styled-components.com/atom.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">styled-components</h1>
		<p class="rich-link-card-description">
		Visual primitives for the component age. Use the best bits of ES6 and CSS to style your apps without stress 💅🏾
		</p>
		<p class="rich-link-href">
		https://styled-components.com/
		</p>
	</div>
</a></div>

- lets you attach styles to components using template strings
    - you can use Sass-style nesting in style strings
        - a single `&` refers to all instances of the component, a double `&&` refers to only the current instance

```jsx
import styled from 'styled-components'

const Header = styled.h1`
    font-size: 1.5em;
    color: deepskyblue;

    &::before {
        content: '# ';
    }

    span {
        color: red;
    }
`

export default function Page() {
    return (
        <Header>Hello world! <span>This text will be red</span></Header>
    )
}
```

> [!important]
> Be sure to define styled components outside of your render function, or they will be recreated on every render!

- to extend an existing component's styles, pass it to the `styled` constructor

```jsx
const Button = styled.button`
    background-color: gray;
    color: white;
`

const PrimaryButton = styled(Button)`
    background-color: darkblue;
`
```

- you can access props on the styled component using interpolated functions

```jsx
const Button = styled.button`
    background-color: ${props => props.primary ? 'deepskyblue' : 'white'};
`

export default function Popup() {
    return (
        <Button>Cancel</Button>
        <Button primary>OK</Button>
    )
}
```

- you can set props on the styled component using `styled.attrs`
- to make a styled component render with a different tag, use the `as` prop

```jsx
const Header = styled.h1`
    color: gray;
`

const SubHeader = styled(Header).attrs(props => ({
    as: 'h2',
    someOtherProp: 123
}))`
    color: darkgray;
`

export default function Page() {
    return (
        <Header>Heading 1</Header>
        <SubHeader>Heading 2</SubHeader>
        <SubHeader as="h3">Heading 3</SubHeader>
    )
}
```

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
- Make sure that children have a `key`, and functional components are wrapped in [[Development/Cheat sheets/React cheat sheet#forwardRef\|#forwardRef]]

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

- [[Development/Cheat sheets/React Native cheat sheet\|React Native cheat sheet]]
- [[Development/Cheat sheets/Next.js cheat sheet\|Next.js cheat sheet]]
