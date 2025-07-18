---
{"dg-publish":true,"dg-path":"Cheat sheets/Vue.md","permalink":"/cheat-sheets/vue/","tags":["language/vue"]}
---


> [!attention]
> These notes are written for Vue 2. Also see [[Development/Cheat sheets/Vue 3\|Vue 3]]

# General

- Prop and event names should be written in the template in kebab-case

> [!warning] Common pitfalls
> - props default to optional unless `required: true` is specified!
> - computed property getters should not have side effects!
> - don't mix `v-if` and `v-for` on the same element!
> - don't change a value that's used in a `v-if` inside the `beforeMount()` hook - this can cause the DOM element to unload during render and cause an error!
>     - use `mounted()` instead, or a [[#Nuxt|Nuxt hook]] if you're using Nuxt and the value can be loaded server-side

# Reactivity

## Adding or removing reactive object properties

- To add or delete properties on a `data` object, use `this.$set(object, property, value)` or `this.$delete(object, property)`
    - You can't add or delete properties directly on `data`

# Watchers

## Use a component method with a watcher

- To use a method from `methods` as a watch handler, pass the name as a string

```js
watch: {
    foo: 'fooChanged'
},
methods: {
    fooChanged() {
        this.$track('foo', this.foo)
    }
}
```

## Don't use arrow functions for watchers

- If you use an arrow function as a watch handler, you won't have access to the component as `this`

## Immediate and deep watchers

- `immediate` calls the handler as soon as observation starts (usually when the component is mounted)
- `deep` watches nested object properties
    - you can also use a string like `bar.baz` to watch a property of an object

```js
watch: {
    foo: {
        handler(newVal, oldVal) {
            ...
        },
        immediate: true
    },
    'bar.baz': function (newVal, oldVal) {
        // triggers when property 'baz' of 'bar' changes
    },
    bar: {
        handler(newVal, oldVal) {
            // triggers when `bar` changes, or `bar.baz`, or `bar.baz.abc`
        },
        deep: true
    },
}
```

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

## Apply deep scoped (global) styles using `::v-deep`

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

- can be bound to non-string types or reactive values using `v-bind` or `:true-value`

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

- the component with the slot uses a `<slot>` element, the parent component inserts content with `<template #slotName>`
    - the `<slot>` element can contain default content

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

## Setup

- `vue-shim.d.ts`

```ts
import Vue from Vue
import type Axios from 'axios'
import type { NuxtCookies } from 'cookie-universal-nuxt'

declare module '*.vue' {
	export default Vue
}

// type plugins
declare module 'vue/types/vue' {
	interface Vue {
		$axios: Axios
	}
}

// type Nuxt context
declare module '@nuxt/types' {
	interface Context {
		$cookies: NuxtCookies
	}
}
```

- inside your components:

```html
<script lang="ts">
import { defineComponent, PropType } from 'vue'
import type { MetaInfo } from 'vue-meta' // if using Nuxt
import type { User } from '~/types/types'

export default defineComponent({
  head(): MetaInfo {
    /* ... */
  },
  props: {
    user: {
      type: Object as PropType<User>
    }
  }
})
</script>
```

## Ignore template errors

- in Vue 2, use `<!-- @vue-ignore -->` to ignore TypeScript errors in the template
- Vue 3 supports TS syntax in the template so this isn't needed

```html
<!-- @vue-ignore -->
<input type="text" @change="handleChange($event.currentTarget.value)"></input>
```

# Nuxt

> [!attention]
> These notes are written for Nuxt 2.

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

- [[Development/Cheat sheets/Vue 3\|Vue 3]]
