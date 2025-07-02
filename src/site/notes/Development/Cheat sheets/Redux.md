---
{"dg-publish":true,"dg-path":"Cheat sheets/Redux.md","permalink":"/cheat-sheets/redux/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://redux.js.org/tutorials/fundamentals/part-2-concepts-data-flow" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://redux.js.org/img/redux-logo-landscape.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Redux Fundamentals, Part 2: Concepts and Data Flow | Redux</h1>
		<p class="rich-link-card-description">
		The official Redux Fundamentals tutorial: learn key Redux terms and how data flows in a Redux app
		</p>
		<p class="rich-link-href">
		https://redux.js.org/tutorials/fundamentals/part-2-concepts-data-flow
		</p>
	</div>
</a></div>

# Selectors

- the component will re-render whenever anything returned by `useAppSelector` changes, so only return what you need
    - the check is done referentially, so avoid returning wrapper objects or arrays (since they will be re-created each time the selector function runs), unless you use a [[#Memoized selectors|memoized selector]]
- it's okay for selectors to derive computed values from the state
- returned values are referentially equal if the value in the state hasn't changed

```js
/* do this */
const foo = useAppSelector((state) => state.foo)
const baz = useAppSelector((state) => state.bar.baz)

/* don't do this - this will re-render when anything in the state changes */
const {
    foo,
    bar: { baz }
} = useAppSelector((state) => state)

/* don't do this - this will re-render every time an action is dispatched, since the returned object isn't referentially the same */
const { foo, baz } = useAppSelector((state) => ({
    foo: state.foo,
    baz: state.bar.baz
}))
```

## Memoized selectors

- use the `createSelector` function from `@reduxjs/toolkit` to create a memoized selector
- all but the last argument are regular selectors that follow the rules above
- the last argument is a selector that takes the results of all the previous selectors and returns a memoized value

```js
import { createSelector } from '@reduxjs/toolkit'

export const selectTodoIds = createSelector(
  // First, pass one or more "input selector" functions:
  state => state.todos,
  // Then, an "output selector" that receives all the input results as arguments
  // and returns a final result value
  todos => todos.map(todo => todo.id)
)
```

# Actions

- plain objects with a `type` field which describes something happening, and an optional `payload` with more info

```js
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```

- usually created with an *action creator* function

```js
const todoAdded = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}

store.dispatch(todoAdded('Buy milk'))
```

# Reducers

- functions that take the current state and an [[#Actions|action object]], and return the new state
- reducers should:
    - be pure and synchronous (use [[#Thunks (async dispatch)|thunks]] for async logic and side effects)
    - only calculate the new state based on the current state and action (nothing external)
    - either return the current state unchanged, or return a new copy of the state with the necessary changes (don't mutate the current state)
- reducers can call other reducers

# Dispatch

- function that takes an action object and uses it to update the state

```js
store.dispatch({ type: 'counter/incremented' })
```

- `useDispatch` hook returns the dispatch function in a React component
    - `useAppDispatch` is the typed version
- dispatches are immediate - if you update state with `dispatch` and then get that state with `useAppSelector` in the same render, you will get the new state

# Thunks (async dispatch)

- `redux-thunk` middleware lets you intercept calls to [[#Dispatch|dispatch]] in order to run async logic
- instead of an [[#Actions|action object]], call `dispatch` with a function called a *thunk action creator*, which is a function that returns another function called the *thunk function*
    - the thunk function gets the `dispatch` function and current state as arguments
    - the thunk function can be async and have side effects

```js
// fetchTodoById is the "thunk action creator"
export function fetchTodoById(todoId) {
  // fetchTodoByIdThunk is the "thunk function"
  return async function fetchTodoByIdThunk(dispatch, getState) {
    const response = await client.get(`/fakeApi/todo/${todoId}`)
    dispatch(todosLoaded(response.todos))
  }
}

store.dispatch(fetchTodoById(123))
```

# createSlice

- accepts a slice name, an initial state, and an object of [[#Reducers|reducers]], and automatically generates [[#Actions|action]] creators and types
- each reducer function gets a proxy state object (for the slice's state, not the whole store) and an action object
    - reducers can either update the proxy state **or** return a new state object, but not both

```js
import { createSlice } from '@reduxjs/toolkit'
import type { PayloadAction } from '@reduxjs/toolkit'

interface CounterState {
  value: number
}

const initialState = { value: 0 } satisfies CounterState as CounterState

const counterSlice = createSlice({
  name: 'counter',
  initialState,
  reducers: {
    increment(state) {
      state.value++
    },
    decrement(state) {
      state.value--
    },
    incrementByAmount(state, action: PayloadAction<number>) {
      state.value += action.payload
    },
  },
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions
export default counterSlice.reducer
```

- to create [[#Thunks (async dispatch)|thunks]], set `reducers` to a function that takes a `create` object
    - `create.asyncThunk` takes a function that receives the dispatch payload and performs async operations, and an object with handlers for different promise lifecycle states

```js
// error handling omitted
reducers: (create) => {
    increment: create.reducer((state) => state.value++),
    fetchValue: create.asyncThunk(async (payload) => {
        return {
            response: await fetch('/value', {
                method: 'POST',
                body: payload.body
            }).then((response) => response.json())
        }
    }, {
        pending: (state) => { /* ... */ },
        fulfilled: (state, action) => {
            state.value = action.payload.response
        },
        rejected: (state, action) => { /* ... */ },
        settled: (state, action) => { /* ... */ },
    })
}
```

- to use `create.asyncThunk`, replace `createSlice` with a custom version:

```js
import { buildCreateSlice, asyncThunkCreator } from '@reduxjs/toolkit'

export const createAppSlice = buildCreateSlice({
  creators: { asyncThunk: asyncThunkCreator },
})
```
