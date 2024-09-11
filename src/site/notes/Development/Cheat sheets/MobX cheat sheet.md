---
{"dg-publish":true,"dg-path":"Cheat sheets/MobX cheat sheet.md","permalink":"/cheat-sheets/mob-x-cheat-sheet/","tags":["language/react"]}
---


# Basics

- `npm install --save mobx mobx-react-lite`
    - `mobx-react-lite` only supports function components, `mobx-react` supports class components too
- the advantage over [[Development/Cheat sheets/React cheat sheet#Context\|contexts]] is that a component using a context will re-render if **anything** in that context changes, but MobX stores only cause re-render if a piece of state the component is actually using (including individual items in collections) changes
- three main concepts:
    - *state*: values you want to observe
    - *actions*: methods that modify state
    - *derivations*: anything that can be derived from the state
        - *computed values*: derived from the current state with no side effects
        - *reactions*: side effects that run whenever the state changes, should be used sparingly
- stick to [[Development/Cheat sheets/React cheat sheet#useState\|useState]] for local component state, unless you need deep watching or computed values
- grab values from objects as late as possible (close to when they're going to be rendered into the DOM)

# Creating stores

- the easiest way to set up a store is to create a class, and use `makeObservable` to "mark" the properties and actions that should be observable
- you can also use `makeAutoObservable` to make all the properties of the given object observable
    - getters will become computed values
    - you can pass overrides as the second argument, the same as `makeObservable` (pass `false` for a property to ignore it)

```js
import { action, makeObservable, observable } from 'mobx'

class TodoStore {
    todos = []

    constructor() {
        makeObservable(this, {
            todos: observable,
            addTodo: action
        })
        /* or makeAutoObservable(this) */
    }

    get incompleteTodos() {
        return this.todos.filter(t => !t.complete)
    }

    addTodo(name) {
        this.todos.push({ name, complete: false })
    }
}
export default new TodoStore()
```

- wrap React components that depend on state in `observer` to make them reactive
    - you can also use the `<Observer>` component, with the child being a render function for your component

```tsx
import { observer } from 'mobx-react-lite'

export default observer(function TodoList() {
    /* ... */
}
```

- complex apps will often have a store for UI state (which can be passed throughout the application using [[Development/Cheat sheets/React cheat sheet#Context\|Context]], and individual stores for different data domains (ex. todos, users, etc.)
- to let stores access each other, you can create a RootStore that holds references to all the other stores

```js
class RootStore {
    constructor() {
        this.userStore = new UserStore(this)
        this.todoStore = new TodoStore(this)
    }
}

class UserStore {
    constructor(rootStore) {
        this.rootStore = rootStore
    }

    getTodos(user) {
        // Access todoStore through the root store.
        return this.rootStore.todoStore.todos.filter(todo => todo.author === user)
    }
}

class TodoStore {
    todos = []
    rootStore

    constructor(rootStore) {
        makeAutoObservable(this)
        this.rootStore = rootStore
    }
}
```

# Persistent stores

- `npm install --save mobx-persist-store`
