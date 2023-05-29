---
{"dg-publish":true,"dg-path":"Cheat sheets/Vue 3 cheat sheet.md","permalink":"/cheat-sheets/vue-3-cheat-sheet/"}
---


# Composition API

```html
<script setup lang="ts">
// code goes here!
</script>
```

## Why use composition?

> [!important] The Composition API is recommended over mixins.

- solves the drawbacks of mixins:
    - unclear which mixin a property comes from
    - namespace collisions are possible
    - if multiple mixins need to interact they need to share state
        - with composables, everything is passed via arguments
- logical concerns can be grouped together
    - easier to extract logic into another component or utility
- better type inference
- more efficient compilation

## Reactive State & Refs

> [!note]
> It seems to be simplest to default to `ref`, unless there's a specific reason to use `reactive`.

- for object types (including arrays & collection types), use `reactive` to create a proxy of the object with reactive properties
    - newly added properties will be reactive (no need for Vue.set)

```js
import { reactive } from 'vue'

const state = reactive({ count: 0 })

function increment() {
    state.count++
}
```

- anything that changes the reference to the `reactive` object will break the reactivity connection, including all of the following:

```js
/* do not do any of these! */

// code that uses the first reference will no longer track changes
let state = reactive({ count: 0 })
state = reactive({ count: 1 })

// count will not be reactive
const { count } = reactive({ count: 0 })

// the function will receive a plain number that won't react to changes
callFunction(state.count)
```

- for value types, use `ref`, which wraps the value in an object with a `.value` property
    - object types can be stored in `ref`s as well, and replaced without the wrapper losing reactivity

```js
import { ref } from 'vue'

const count = ref(0)

console.log(count.value)

// this is fine!
const objRef = ref({ count: 0 })
objRef.value = { count: 1 }
```

- `ref`s are unwrapped when accessed as **top-level** properties in the template, so you don't need to use `.value`

```html
<template>
    <h1>{{count}}</h1>
</template>
```

- `ref`s are also unwrapped when used as properties of `reactive` objects, but not `reactive` arrays or collection types

```js
const count = ref(0)
const state = reactive({ count })

console.log(state.count) // don't need .value

const array = reactive([ref('Hello')])
console.log(array[0].value)
```

### Reactivity Transform

- still experimental, must be [opted into](https://vuejs.org/guide/extras/reactivity-transform.html#explicit-opt-in)
- create reactive variables that don't need `.value` by using `$ref()`
    - also `$computed`, `$shallowRef`, `$customRef`, `$toRef`

```js
let count = $ref(0)
console.log(count) // OK
count++ // OK
```

- destructure reactive objects using `$()`
    - can also convert existing `ref`s into reactive variables

```js
const { x, y } = $(useMouse())

const createRef = () => ref(0)
let count = $(createRef())
```

- allows you to destructure props with no additional code, and set default values easier when using [[Development/Cheat sheets/Vue 3 cheat sheet#^04077c\|type-based declaration]]

```ts
interface Props {
    msg: string
    count?: number
    foo?: string
}

const {
    msg,
    count = 1,
    foo: bar
} = defineProps<Props>()
```

- passing reactive variables to functions won't work, because it will pass the value instead of the `ref`
- to fix this, wrap the variable in `$$()` to preserve the `ref`

```js
let count = $ref(0)
trackChange($$(count))
```

- same with returning `ref`s from functions - you can use `$$()` on an object of refs

```js
function useMouse() {
    let x = $ref(0)
    let y = $ref(0)

    return $$({
        x,
        y,
    })
}
```

- these compiler macros don't need to be imported, but you can import them from `vue/macros` if desired
    - may also need to set this eslint rule to suppress error when destructuring props

```js
rules: {
  'vue/no-setup-props-destructure': 0,
},
```

## Computed Properties

- `computed` returns a `ref`

```js
import { reactive, computed } from 'vue'

const person = reactive({ firstName: 'John', lastName: 'Smith' })

const fullName = computed(() => {
    return person.firstName + ' ' + person.lastName
})

console.log(fullName.value)
```

- to provide a setter:

```js
const fullName = computed({
    get() {
        ...
    },
    set(newValue) {
        ...
    }
})
```

## Lifecycle Hooks

- same names as Vue 2, but begin with `on`
    - exceptions: `beforeDestroy` -> `onBeforeUnmount`, `destroyed` -> `onUnmounted`
- must be registered synchronously

```js
import { onMounted, onUpdated, onUnmounted } from 'vue'

onMounted(() => { ... })

// this won't work
setTimeout(() => {
    onMounted(() => { ... })
}, 100)
```

## Watchers

- you can't `watch` a property of a reactive object (because the reference is not reactive), instead use a getter

```js
import { reactive, watch } from 'vue'

const obj = reactive({ count: 0 })

// this won't work
watch(obj.count, (newCount, oldCount) => { ... })

// instead do this
watch(() => obj.count, (newCount, oldCount) => { ... })
```

- you can watch multiple sources with an array

```js
const x = ref(0)
const y = ref(0)

watch([x, y], ([newX, newY]) => { ... })
```

- watchers directly on reactive objects are deep, but watchers that use getters are not - to rectify this use the `deep` option

```js
watch(
    () => state.someObject,
    (newValue, oldValue) => {
        // if deep is not used, won't trigger unless state.someObject is replaced
    },
    { deep: true }
)
```

- create eager/immediate watchers using `watchEffect`
    - automatically tracks dependencies just like computed properties
        - only tracks dependencies during synchronous execution - if used with an `async` callback, only dependences accessed before the first `await` are tracked

```js
import { watchEffect } from 'vue'
const url = ref('https://...')
const data = ref(null)

watchEffect(async () => {
    // will run on mount and when url changes
    const response = await fetch(url.value)
    data.value = await response.json()
})
```

- watchers are called before the DOM updates - if you need to access the updated DOM inside a watcher, use the `flush: post` option or `watchPostEffect()`
- to stop a watcher, use the function returned from `watch` or `watchEffect`

```js
const unwatch = watchEffect(() => { ... })
unwatch() // later
```

- watchers can be created in async callbacks, but won't be stopped automatically when the owner unmounts, so must be stopped manually
    - prefer making the watch logic conditional instead

```js
const data = ref(null)

getAsyncData.then(result => {
    data.value = result
    // don't do this
    watch(data, (newData) => { ... })
})

// instead do this
watch(data, (newData) => {
    if (newData.value) {
        ...
    }
})
```

## Template Refs

- declare a template `ref` with the same name as the `ref` attribute (don't need v-bind)
    - `ref`s are `null` on first render

```html
<input ref="input">
```

```js
import { ref } from 'vue'
const input = ref(null)
console.log(input.value)
```

- when using `ref` with `v-for`, pass in an Array which will be populated with the references after mount
    - the order is **not** guaranteed to be the same as the source array order

```html
<li v-for="item in list" ref="itemRefs">{{ item }}</li>
```

```js
const list = ref([ ... ])
const itemRefs = ref([])
```

- `ref` can also be bound to a function that will run on each component update, and receives the reference as the first argument

```html
<input :ref="(el) => { /* assign el to a property or ref */ }">
```

- referenced components using `<script setup>` are **private by default**, so the `ref` cannot access anything unless the component exposes it using the `defineExpose` macro
    - macros do not need to be `import`ed

```js
/* child component */
import { ref } from 'vue'

const a = 1
const b = ref(2)

defineExpose({ a, b })

/* parent component */
// child has the shape { a: number, b: number }
const child = ref(null)
```

## Props

- define props using the `defineProps` macro, which returns an object with all the passed prop values
    - or use [[Development/Cheat sheets/Vue 3 cheat sheet#^04077c\|type-based declaration]]
    - code inside `defineProps` can't access other variables declared in `<script setup>`

```js
const props = defineProps({
    author: String,
    year: [String, Number], // can be either
    title: {
        type: String,
        required: true
    },
    type: {
        type: String,
        default: 'Nonfiction',
        validator(value) {
            return ['Fiction', 'Nonfiction'].includes(value)
        }
    },
    genres: {
        type: Array,
        default(rawProps) {
            // object or array defaults must be returned from a factory function, which receives the raw props as an argument
            return ['adventure', 'sci-fi']
        }
    }
})
console.log(props.title)
```

## Events (emits) and v-model

- to emit events in inline handlers, use `$emit`
- events should be documented using the `defineEmits` macro, which returns an `emit` function that can be used in the script
    - or use [[Development/Cheat sheets/Vue 3 cheat sheet#^aa6726\|type-based declaration]]

```js
const emit = defineEmits({
    text: String,
    click: null, // no validation
    submit({ email, password }) => {
        return email && password
    }
})

emit('submit', { email, password })
```

- to set up a `v-model` on a component, use a prop named `modelValue` and an `update:modelValue` event
    - to change the name of the prop, pass an argument to the `v-model` directive - this lets you have multiple `v-model`s on one component
        - this replaces the `.sync` modifier on `v-bind`

```html
<ChildComponent v-model:title="pageTitle" />

<!-- is shorthand for: -->

<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

- `v-model` can support [custom modifiers](https://vuejs.org/guide/components/events.html#handling-v-model-modifiers)

## Fallthrough Attributes

- to forward non-prop attributes (including `class` and `style`) and listeners to an element that isn't the root, set `inheritAttrs: false` and use `$attrs`
    - components with multiple root nodes have `inheritAttrs` off by default

```html
<div class="btn-wrapper">
    <button v-bind="$attrs">Click me</button>
</div>
```

```js
<script>
// use a normal <script> block to declare options
export default {
    inheritAttrs: false
}
</script>

<script setup>
// ...setup logic here
</script>
```

- fallthrough attributes can be accessed in the script using `useAttrs()` (note that this isn't reactive)

## Provide and Inject

- provide key can be a string or Symbol
    - if you are working in a large application, use Symbol keys to avoid collisions, and export/import the keys from a dedicated file
    - Symbol keys also [[Development/Cheat sheets/Vue 3 cheat sheet#^2e8115\|work better in TypeScript]]
- provide value can be anything, including reactive state
- injected `ref`s are **not** automatically unwrapped

```js
import { provide } from 'vue'
provide('message', 'hello!')

// in another component

import { inject } from 'vue'
const message = inject('message')
```

- specify a default value for `inject`
    - if the default value is expensive, create it in a factory function

```js
const value = inject('message', 'default value')
const value2 = inject('key', () => new ExpensiveClass())
```

- **keep any state mutations inside the provider if possible**
- if you need to update the provided state from an injector component, `provide` a function to mutate it

```js
// provider component
const location = ref('North Pole')
function updateLocation(newLocation) {
    location.value = newLocation
}
provide('location', { location, updateLocation })

// injector component
const { location, updateLocation } = inject('location')
```

- wrap provided value with `readonly` to ensure injector components can't mutate it

```js
import { ref, provide, readonly } from 'vue'
const count = ref(0)

provide('read-only-count', readonly(count))
```

## Composables

- always start with `use` (like [[Development/Cheat sheets/React cheat sheet#Hooks\|React hooks]])
- should only be called synchronously
- composables can call other composables, to break down logic into small, isolated units
- instead of returning a `reactive`, return a plain object containing multiple `ref`s, so that it can be destructured
- make sure to clean up side effects using `onUnmounted`

```js
import { ref, onMounted, onUnmounted } from 'vue'
import { useEventListener } from './eventListener.js'

export function useMouse() {
    const x = ref(0)
    const y = ref(0)

    function update(event) {
        x.value = event.pageX
        y.value = event.pageY
    }

    // see below
    useEventListener(window, 'mousemove', update)

    // state must be returned
    return { x, y }
}

// in a component:

import { useMouse } from './mouse.js'

const { x, y } = useMouse()
// or
const mouse = reactive(useMouse())
console.log(mouse.x)
```

- example event listener component:

```js
import { onMounted, onUnmounted } from 'vue'

export function useEventListener(target, event, callback) {
    onMounted(() => target.addEventListener(event, callback))
    onUnmounted(() => target.removeEventListener(event, callback))
}
```

- composables can accept arguments, including `ref`s, and use `watchEffect` to re-run code when the `ref` changes

```js
import { ref, isRef, unref, watchEffect } from 'vue'

export function useFetch(url) {
    const data = ref(null)
    const error = ref(null)

    function doFetch() {
        data.value = null
        error.value = null

        // unref() unwraps potential refs
        fetch(unref(url))
            .then(res => res.json())
            .then(json => (data.value = json))
            .catch(err => (error.value = err))
    }

    if (isRef(url)) {
        // set up reactive re-fetch if input URL is a ref
        watchEffect(doFetch)
    } else {
        doFetch()
    }

    return { data, error }
}
```

## Directives

- objects that start with `v` and have specific hooks

```js
<script setup>
const vFocus = {
    mounted: el => el.focus()
    //// other hooks:
    // created
    // beforeMount
    // beforeUpdate
    // updated
    // beforeUnmount
    // unmounted
}
</script>

<template>
    <input v-focus />
</template>
```

- directive hooks get the following arguments:
    - `el`: the DOM element
    - `binding`: an object with these properties:
        - `value`: always treated as an expression (ex. if `v-directive="1+1"` then `value === 2`)
            - can be an object
        - `oldValue`: the previous value, only in `beforeUpdate` and `updated`
        - `arg`: ex. `v-directive:arg`
        - `modifiers`: ex. `v-directive.foo.bar` -> `{ foo: true, bar: true }`
        - `instance`: the component instance
        - `dir`: the directive object
    - `vnode`: the VNode of the bound element
    - `prevNode`: the VNode of the previous render, only in `beforeUpdate` and `updated`
- hook arguments (except for `el`) are read-only - to share information across hooks use the element's `dataset`
- directive arguments can be dynamic

```html
<div v-example:[arg]="value"></div>
```

- directives that have the same behavior for `mounted` and `updated`, and no other hooks, can be declared as a function

```js
const vColor = (el, binding) => {
    el.style.color = binding.value
}
```

- it is recommended **not** to use custom directives on components - directives always apply to a component's root node and can't be forwarded (unlike [[Development/Cheat sheets/Vue 3 cheat sheet#Fallthrough Attributes\|#Fallthrough Attributes]])

## Plugins

```js
import { createApp } from 'vue'
const app = createApp({})

const myPlugin = {
    install(app, options) {
        // global methods
        app.config.globalProperties.$translate = key => { ... }

        // global `provide` values
        app.provide('message', 'hello!')

        // global components
        app.component('my-component', /* imported component */ )

        // global directives
        app.directive('my-directive', /* directive object or function */ })
    }
}

app.use(myPlugin, {
    /* plugin options go here */
})
```

## TypeScript typing

### Refs and computed

- `ref`s and `computed` properties have inferred types, but can be typed explicitly by passing a generic argument to the creation function
    - omit the initial value to create an optional ref

```ts
const year = ref<string | number>('2020')

const double = computed<number>(() => { ... })
```

- template `ref`s should be explicitly typed

```html
<script setup lang="ts">
const el = ref<HTMLInputElement | null>(null)
</script>

<template>
    <input ref="el" />
</template>
```

- type component `ref`s like this

```ts
const modal = ref<InstanceType<typeof MyModal> | null>(null)
```

### Reactive

- `reactive` objects also have inferred types, or can be typed using interfaces
    - don't use the generic argument for `reactive`

```ts
interface Book {
    title: string
    year?: number
}
const book: Book = reactive({ title: 'Vue 3 Guide' })
```

### Props

- prop types can be inferred from `defineProps`, or using a generic argument (type-based declaration)
{ #04077c}

    - if using type-based declaration:
        - generic argument must be either an inline object literal type, or a reference to an interface or object literal type **in the same file** (though it can reference types imported from elsewhere)
        - defaults must be passed via the `withDefaults` macro

```ts
export interface Props {
    msg?: string
    labels?: string[]
}

const props = withDefaults(defineProps<Props>(), {
    msg: 'hello',
    labels: () => ['one', 'two']
})
```

### Events (emits)

- event types can be declared using a generic argument on `defineEmits`
{ #aa6726}


```ts
const emit = defineEmits<{
    (e: 'change', id: number): void
    (e: 'update', id: string, text: string): void
}>()
// equivalent to: defineEmits(['change', 'update'])
```

- explicitly type the event argument in event handler functions
    - you may need to cast properties on the event

```ts
function handleChange(event: Event) {
    console.log((event.target as HTMLInputElement).value)
}
```

### Provide and Inject

- for `provide`/`inject`, use the `InjectionKey` interface with Symbol keys
{ #2e8115}


```ts
const key = Symbol() as InjectionKey<typeof foo>
provide(key, foo)
```

# Transition/TransitionGroup

![transition-classes.f0f7b3c9.png](/img/user/%E2%80%A2%20Attachments/transition-classes.f0f7b3c9.png)

- example for fade in/out
    - if the `<Transition>` component has a `name` prop, `v` will be replaced by the name

```css
.v-enter-active, .v-leave-active {
    transition: opacity 0.5s ease;
}

.v-enter-from, .v-leave-to {
    opacity: 0;
}
```

````ad-example
title: JavaScript Hooks
collapse: closed

> [!tip]
> If using JavaScript-only transitions, add the prop `:css="false"`

```html
<Transition
  @before-enter="onBeforeEnter"
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @enter-cancelled="onEnterCancelled"
  @before-leave="onBeforeLeave"
  @leave="onLeave"
  @after-leave="onAfterLeave"
  @leave-cancelled="onLeaveCancelled"
>
  <!-- ... -->
</Transition>
```
---
```js
// called before the element is inserted into the DOM.
// use this to set the "enter-from" state of the element
function onBeforeEnter(el) {}

// called one frame after the element is inserted.
// use this to start the entering animation.
function onEnter(el, done) {
  // call the done callback to indicate transition end
  // optional if used in combination with CSS
  done()
}

// called when the enter transition has finished.
function onAfterEnter(el) {}
function onEnterCancelled(el) {}

// called before the leave hook.
// Most of the time, you should just use the leave hook
function onBeforeLeave(el) {}

// called when the leave transition starts.
// use this to start the leaving animation.
function onLeave(el, done) {
  // call the done callback to indicate transition end
  // optional if used in combination with CSS
  done()
}

// called when the leave transition has finished and the
// element has been removed from the DOM.
function onAfterLeave(el) {}

// only available with v-show transitions
function onLeaveCancelled(el) {}
```

````

- add the `appear` prop to make the transition apply on first render
- `<Transition>`s can only apply to one element at a time, but this can include switching between elements using `v-if`
    - set `mode="out-in"` to make the leaving element animate out before the entering element animates in
        - not available for `<TransitionGroup>`
- create reusable transitions by wrapping the `<Transition>` component

```html
<template>
    <Transition name="my-transition">
        <slot></slot>
    </Transition>
</template>
```

# Teleport

```html
<Teleport to="body" :disabled="isMobile">
  <div v-if="open" class="modal">
    <p>Hello from the modal!</p>
    <button @click="open = false">Close</button>
  </div>
</Teleport>
```

- the `to` target must already be in the DOM when the `Teleport` component is mounted
- `Teleport` does not affect the **logical** hierarchy of components, only the DOM output
- if multiple `Teleport`s have the same target, the contents will be appended

# Suspense (experimental) and Async Components

- to load a component async (only once it is rendered)
    - props and slots on AsyncComp are passed to the inner component

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent({
    loader: () => import('/path/to/component.vue'),
    // component to show while the async component is loading
    loadingComponent: LoadingComponent,
    // delay in ms before showing the loading component (default: 200)
    delay: 200,

    // component to show if loading fails
    error: ErrorComponent,
    // delay in ms before showing the error component (default: Infinity)
    timeout: 3000,
})
```

- `<Suspense>` lets you display top-level loading and error states while you wait for nested async dependencies to resolve
    - in other words, you can have one loading spinner while you wait for multiple components to load
    - supports components with an async `setup()` hook (including `<script setup>` components with top-level await expressions), and async components
- async components that have a `<Suspense>` in their parent chain are treated as a dependency of it, and the async component's own loading, error, delay, & timeout options are ignored, unless it has `suspensible: false` in its options

```html
<Suspense>
    <!-- async content goes in the default slot -->
    <!-- each slot can only have one immediate child -->
    <Dashboard />

    <!-- loading state goes in the fallback slot -->
    <template #fallback>
        Loading...
    </template>
</Suspense>
```

- `<Suspense>` will only return to a pending state if the root node is replaced - new nested async dependencies will not trigger the fallback content again
- if root node is replaced, `<Suspense>` will keep rendering the old content - to render the fallback content instead set a `timeout` prop
- `<Suspense>` emits `pending`, `resolve`, and `fallback` events
- use the `onErrorCaptured()` hook to handle async errors
- must be nested in the following order:

```html
<RouterView v-slot="{ Component }">
  <template v-if="Component">
    <Transition mode="out-in">
      <KeepAlive>
        <Suspense>
          <!-- main content -->
          <component :is="Component"></component>

          <!-- loading state -->
          <template #fallback>
            Loading...
          </template>
<!-- close everything -->
```

# Router

- to access the router in a `script setup` component
    - you can still use `$route` in the template

```js
import { useRouter, useRoute } from 'vue-router'

const router = useRouter()
const route = useRoute()
```

# Stores (Pinia)

- define a store using `defineStore`
    - return anything that needs to be exposed

```js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  const count = ref(0)
  const doubleCount = computed(() => count.value * 2)
  function increment() {
    count.value++;
  }

  return { count, doubleCount, increment }
});
```

- you cannot destructure reactive properties from the store, but you can destructure actions
- use `storeToRefs` to extract `ref`s for each reactive property

```js
import { storeToRefs } from 'pinia'
import { useCounterStore } from './stores/counter'

const store = useCounterStore()
const { count, doubleCount } = storeToRefs(store)
const { increment } = store

console.log(count)
increment()
```

> [!important]
> The store is just a `reactive` object, so you **can** modify the store state directly!
> 
> ```js
> count++ // this is fine!
> ```

- to use a store inside another store, `use` it inside one of the store functions

```js
export const useSettingsStore = defineStore('settings', () = {
    async function fetchUserPreferences() {
        const auth = useAuthStore()
        if (auth.isAuthenticated) {
            ...
        } else {
            throw new Error('User must be authenticated')
        }
    }
})
```

# Other Changes

## Template

- can use `v-for` on `Map`s

```html
<Item v-for="[key, value] in items">
    {{value}}
</Item>
```

## Script

- components can have multiple root nodes
    - non-prop attributes must be [[Development/Cheat sheets/Vue 3 cheat sheet#Fallthrough Attributes\|forwarded to a specific node]]
- `nextTick` is now imported from `vue`
- `beforeDestroy` and `destroyed` are renamed to `beforeUnmount` and `unmounted`
    - when using composition, prefix with `on`
- `v-bind.sync` is replaced with [[Development/Cheat sheets/Vue 3 cheat sheet#Events (emits) and v-model\|v-model]]
- [[Development/Cheat sheets/Vue 3 cheat sheet#Provide and Inject\|#Provide and Inject]] bindings are reactive

## Style

- component state can be referenced in `<style>` blocks
    - JavaScript expressions must be wrapped in quotes

```css
.text {
  color: v-bind(color);
}

p {
  color: v-bind('theme.color');
}
```

- target slotted content (in the child component) using `::v-slotted()`
- apply global rules in scoped style blocks using `::v-global()`

# See also

- [[Development/Cheat sheets/Vue cheat sheet\|Vue cheat sheet]]
