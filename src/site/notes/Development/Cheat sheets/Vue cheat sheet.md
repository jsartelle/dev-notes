---
{"dg-publish":true,"dg-path":"Cheat sheets/Vue cheat sheet.md","permalink":"/cheat-sheets/vue-cheat-sheet/"}
---


> [!warning]
> - props default to optional unless `required: true` is specified!
> - computed property getters should not have side effects!
> - don't mix `v-if` and `v-for` on the same element!
> - don't change a value that's used in a `v-if` inside the `beforeMount()` hook - this can cause the DOM element to unload during render and cause an error!
>     - use `mounted()` instead, or a [[Development/Cheat sheets/Vue cheat sheet#Nuxt\|Nuxt hook]] if you're using Nuxt and the value can be loaded server-side

# General

- Prop and event names should be written in the template in kebab-case

# Reactivity

## Adding or removing reactive object properties

- To add or delete properties on a `data` object, use `this.$set(object, property, value)` or `this.$delete(object, property)`
    - You can't add or delete properties directly on `data`

# Conditional Rendering

## Apply `v-if` or `v-for` to multiple elements using `＜template＞`

- does not work with `v-show`

```html
<template>
    <main>
        <template v-if="foo">
            <component :data="foo.bar" />
            <div>{{foo.baz}}</div>
        </template>
    </main>
</template>
```

## Use `v-for` with a range

```html
<span v-for="n in 10">{{ n }}</span>
```

# Class and Style Bindings

```html
<div :class="{ className: propertyName }"></div>
```

## Combine object and array syntax in class bindings

```html
<div :class="[{ className: propertyName }, anotherClassName]"></div>
```

## Apply deep scoped styles using `::v-deep`

```css
/* Vue 2 */
.component ::v-deep a {
	text-decoration: underline;
}

/* Vue 3 */
.component ::v-deep(a) {
	text-decoration: underline;
}
```

# Event Handling

## Access events in inline handlers with `$event`

```html
<button @click="warn('Form cannot be submitted yet.', $event)"
```

## Event modifiers

- keyboard events support any key names exposed by [KeyboardEvent.key](https://developer.mozilla.org/en-US/docs/Web/API/UI_Events/Keyboard_event_key_values) (in kebab-case)
    - on `keyUp` events, modifier keys must be used in conjunction with a "regular" key

```html
<input v-on:keyup.page-down="onPageDown">
```

- `.exact` prevents use of other modifier keys

```html
<!-- will fire if Ctrl and Shift are held -->
<button @click.ctrl="onClick">Button</button>

<!-- will only fire if Ctrl and no other keys are held -->
<button @click.ctrl.exact="onClick">Button</button>

<!-- will only fire if no modifiers are held -->
<button @click.exact="onClick">Button</button>
```

- mouse events support `.left`, `.right`, `.middle`

# Form Input Bindings

## `v-model` Modifiers

- `.trim`: trim input
- `.lazy`: only update on `change` events
- `.number`: cast string to number (or use `type="number"`)

## Bind multiple checkboxes to an array

- also supports sets in Vue 3

```html
<!-- contains the values of the checked boxes -->
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames">
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames">
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames">
<label for="mike">Mike</label>
```

## Provide checkbox values using `true-value` and `false-value`

- can be bound to non-string types or reactive values

```html
<!-- `toggle` will be the string 'yes' or 'no' -->
<input
  type="checkbox"
  v-model="toggle"
  true-value="yes"
  false-value="no" />
```

# Props

## Declare a prop that accepts multiple types

```js
props: {
    year: [String, Number]
}
```

## Spread props object onto component

```js
const props = {
    name: 'Apple',
    color: 'red'
}
```

```html
<Fruit v-bind="props" />
<!-- is equivalent to -->
<Fruit :name="props.name" :color="props.color" />
```

## Two-way binding

Vue 2:

```html
<MyComponent :name.sync="fullName" />
```

Vue 3:

```html
<MyComponent v-model:name="fullName" />
```

Both are shorthand for:

```html
<MyComponent :name="fullName" @update:name="name = $event" />
```

# Slots

```html
<!-- Component.vue -->
<template>
    <slot></slot>
    <slot name="namedSlot"></slot>
</template>

<!-- Page.vue -->
<template>
    <Component>
        <div>Default slot content</div>
        <template #namedSlot>Named slot content</template>
    </Component>
</template>
```

## Bind child props to slots

```html
<!-- Child -->

<div>
    <slot name="text" :message="message" :count="1"></slot>
</div>

<!-- Parent -->

<ChildComponent>
    <template #text="slotProps">{{slotProps.message}}</template>
</ChildComponent>

<!-- or if using the default slot -->

<ChildComponent v-slot="{ message, count }">{{message}}</ChildComponent>
```

# Dynamic Components and KeepAlive

- use the `activated` and `deactivated` lifecycle hooks to listen for de/reactivation
    - also called on mount/unmount respectively

```html
<KeepAlive include="a,b" OR :include="/a|b/" :max="10">
    <component :is="currentTabComponent" />
</KeepAlive>
```

# TypeScript

## With `defineComponent`

```ts
import Axios from 'axios'

// on Vue 3 may only need "declare module 'vue'"
declare module '@vue/runtime-core' {
	interface ComponentCustomProperties {
		$axios: Axios
	}
}
```

## Without `defineComponent`

```ts
import Vue from 'vue'
import Axios from 'axios'

declare module 'vue/types/vue' {
  interface Vue {
    $axios: Axios
  }
}
```

# Nuxt

> [!attention]
> The info below has only been verified with Nuxt 2.

## asyncData hook

- **can only be used on page components**
- runs server side, blocks route navigation until resolved
- merges its return value into the component object
- can't access `this`, gets the [Nuxt context](https://nuxtjs.org/docs/concepts/context-helpers/) as an argument instead

```js
async asyncData({ route }) {
    const posts = await getPostsForRoute(route.name)
    return {
        posts
    }
}
```

## fetch hook

- can be used on any component
- runs server-side when the instance is created, and client-side on navigation
- can set `data` properties like normal (`this.foo = 'bar'`)
- access the [context](https://nuxtjs.org/docs/concepts/context-helpers/) using `this.$nuxt.context`
- can be called any time using `this.$fetch()`

```js
async fetch() {
    this.posts = await getPostsForRoute(this.$nuxt.context.route.name)
}
```

## Load scripts per-page (vue-meta)

```js
head() {
	return {
		script: [
			{
				src: 'https://example.com/script.js',
				callback: () => (this.scriptLoaded = true),
			},
		],
	}
},
```

# Vuex

## Mapping store properties from modules

```js
export default {
    computed: {
        ...mapState({
            userId: (state) => state.user.id
        }),
        ...mapGetters({
            userName: 'user/name'
        })
    },
    methods: {
        ...mapMutations({
            setAddress: 'user/setAddress'
        }),
        ...mapActions({
            sendWelcomeEmail: 'user/sendWelcomeEmail'
        })
    }
}
```

# See also

- [[Development/Cheat sheets/Vue 3 cheat sheet\|Vue 3 cheat sheet]]
