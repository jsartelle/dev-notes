---
{"dg-publish":true,"dg-path":"Cheat sheets/Svelte cheat sheet.md","permalink":"/cheat-sheets/svelte-cheat-sheet/"}
---


# Markup

- JS code in a `.svelte` file goes in the `<script>` tag
    - use `<script lang="ts">` for TypeScript
- expressions in markup and attributes use single curly braces
    - you can use expressions inside quoted string attributes

```html
<script>
	let name = 'world'
	let src = '/tutorial/image.gif'
</script>

<h1>
	Hello {name.toUpperCase()}!
</h1>
<img src={src} alt="Example">
```

- if an attribute name and value are the same, you can leave out the name as a shorthand (**don't forget the curly braces**)

```html
<img {src} alt="Example">
```

## Flow control

- conditionally render using `{#if}`, `{:else if}`, `{:else}`

```html
<script>
	let x = 7
</script>

{#if x > 10}
	<p>{x} is greater than 10</p>
{:else if 5 > x}
	<p>{x} is less than 5</p>
{:else}
	<p>{x} is between 5 and 10</p>
{/if}
```

- use `{#each array as value, index (key)}` to loop over array-like objects
    - index is optional
    - can use destructuring in the value
    - you can use an object as the key, but a primitive is safer

```html
<script>
	let cats = [
		{ id: 'J---aiyznGQ', name: 'Keyboard Cat' },
		{ id: 'z_AbfPXTKms', name: 'Maru' },
		{ id: 'OUtn3pvWmpg', name: 'Henri The Existential Cat' }
	];
</script>

{#each cats as { id, name }, i (id)}
	<li><a target="_blank" href="https://www.youtube.com/watch?v={id}">
		{i + 1}: {name}
	</a></li>
{/each}
```

- repeat a block a certain number of times

```html
{#each { length: buttonCount } as _, i}
    <Button on:click={() => alert(`Button ${i} clicked`)} />
{/each}
```

- await promises directly in markup using `{#await promise}`
    - to show nothing until the promise resolves, use `{#await promise then value}`

```html
{#await getRandomNumber()}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

## Special tags

- a `{#key expression}` block will destroy and re-create its contents (including components) each time `expression` changes
    - useful for playing a transition whenever a value changes

```html
{#key value}
    <div transition:fade>{value}</div>
{/key}
```

- render HTML strings using `{@html string}`

```html
<script>
    const string = 'Here is some <strong>HTML</strong>'
</script>

<p>{@html string}</p>
```

- `{@debug var1, var2, ...}` will log the values of each variable when they change, and launch the debugger if devtools are open
    - use `{@debug}` without arguments to launch the debugger whenever any state changes

- `{@const assignment}` creates a local constant

```html
{#each boxes as box}
    {@const area = box.width * box.height}
    {box.width} * {box.height} = {area}
{/each}
```

## Special elements

- `<svelte:self>` lets a component contain itself (since it can't import itself)
- `<svelte:component>` lets an element render different components based on the `this` prop
    - `this` should be a component constructor
    - if `this` is falsy, nothing will be rendered

```html
<svelte:component this={MyButton} />
```

- `<svelte:element>` is the same, but for elements
    - `this` should be a string with an element name
    - if `this` is nullish nothing will be rendered
    - `this` is the only binding supported on `<svelte:component>`

- Insert elements into the `<head>` using `<svelte:head>`
    - Must appear at the top level

# Styling

- styles go in the `<style>` tag and are scoped
- conditionally add classes using `class:className`

```html
<button class:selected={current === 'foo'}>Click Me</button>
```

- if the class name is the same as the value, you can use shorthand

```html
<script>
    $: const selected = (current === 'foo')
</script>

<button class:selected>Click Me</button>
```

- styles can be added using `style:propName` including custom properties

```html
<script>
    const bgOpacity = 0.5
    const color = blue
</script>

<p style:color style:--opacity={bgOpacity}></p>
```

- apply styles globally using `:global()`
    - prepend keyframe names with `-global-`, but reference them in CSS properties without the prefix

```css
:global(body) {
    background-color: #69F7BE;
}

@keyframes -global-spin { ... }

.square {
    animation: 1s spin;
}
```

# Reactivity

- all variables in the `<script>` block can be used in the template
- declare reactive statements (which re-run when the values they depend on change) using a `$:` label
    - somewhat like [[Development/Cheat sheets/React cheat sheet#useEffect\|useEffect]] in React

```html
<script>
	let count = 0
	$: doubled = count * 2

	function incrementCount() {
		count++
	}
</script>

<button on:click={incrementCount}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
<p>{count} doubled is {doubled}</p>
```

- to type reactive variables, declare them with `let` separately

```js
let doubled: number
$: doubled = count * 2
```

- `$:` will re-run the block whenever any referenced values change, so you can also use it for statements with side effects (like watchers)
- you can group reactive statements into a block
    - if you do this you need to declare any new variables outside the block

```js
let doubled
$: {
    doubled = count * 2
    console.log(`The count is ${count}, which is ${doubled} doubled`)
}
```

- you can mark things like `if` statements with `$:`

```js
$: if (count >= 10) {
    alert('count is dangerously high')
}
```

## Updating arrays & objects

- mutating arrays or objects does not trigger an update - to fix this, assign the object to itself

```js
function addNumber() {
    numbers.push(numbers.length + 1)
    numbers = numbers
    // or numbers = [...numbers, numbers.length + 1]
}
```

- assigning to array or object properties (ex. `object.foo += 1` or `array[i] = x`) *does* trigger update
- indirect assignments *do not* trigger update
    - ==the updated variable must directly appear on the left hand side of the assignment==

```js
const foo = obj.foo
foo.bar = 'baz' // does not trigger update on obj
```

# Components

- component names are always capitalized
- components **don't** need to have a single root element
- import components within the `<script>` tag

```html
<script>
    import Component from './Component.svelte'
</script>

<Component />
```

- components are compiled to regular JavaScript classes, so you can use them outside of Svelte

```js
import App from './App.svelte'

const app = new App({
	target: document.body,
	props: {
		answer: 42
	}
});
```

# Props

- props are declared using `export`
    - assign a value to use it as the default value

```html
<!-- in Nested.svelte -->
<script>
    export let answer = 42
</script>

<!-- in another component -->
<Nested answer={10} />
<Nested /> // answer === 42
```

- you can spread properties onto a component

```html
<Info {...pkg} />
```

- you can access all of a component's props using `$$props`, but this is *not* recommended

# Bindings

## Inputs (Form bindings)

- declare two-way input bindings using `bind:value={var}` (similar to v-model)
    - if the variable name is also `value` you can just use `bind:value`
    - also works with `<select>`, and if the `multiple` attribute is used it will create an array

```html
<script>
	let name = 'world'
</script>

<input bind:value={name}>

<h1>Hello {name}!</h1>
```

- for checkboxes use `bind:checked`
- for radio buttons and grouped checkboxes, use `bind:group` instead of a `name` attribute
    - radio inputs will replace the value, checkboxes will create an array

```html
<script>
	let scoops = 1;
	let flavours = ['Mint choc chip'];

	let menu = [
		'Cookies and cream',
		'Mint choc chip',
		'Raspberry ripple'
	];

	function join(flavours) {
		if (flavours.length === 1) return flavours[0];
		return `${flavours.slice(0, -1).join(', ')} and ${flavours[flavours.length - 1]}`;
	}
</script>

<h2>Size</h2>

<label>
	<input type=radio bind:group={scoops} value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} value={flavour}>
		{flavour}
	</label>
{/each}

{#if flavours.length === 0}
	<p>Please select at least one flavour</p>
{:else if flavours.length > scoops}
	<p>Can't order more flavours than scoops!</p>
{:else}
	<p>
		You ordered {scoops} {scoops === 1 ? 'scoop' : 'scoops'}
		of {join(flavours)}
	</p>
{/if}
```

## Components

- you can bind a prop of a child component to a variable in the parent using `bind:childProp={parentVar}` to allow two-way updating
    - or `bind:propName` if the names are the same
    - use this sparingly to avoid overcomplicating the flow of data!

## this (refs)

- the `this` binding lets you get a reference to an element or component, similar to refs in Vue or React
    - The value will be undefined until mount, so you should only work with the elements inside [[Development/Cheat sheets/Svelte cheat sheet#^23473f\|onMount]] or event handlers

```html
<script>
    import { onMount } from 'svelte'
    import InputField from './InputField.svelte';

    let canvas
    let field

    onMount(() => {
        const ctx = canvas.getContext('2d')
        // do canvas-y stuff
    });
</script>

<canvas bind:this={canvas}></canvas>

<!-- assuming InputField has a `focus` method -->
<InputField bind:this={field} />

<!-- you can't simply pass `field.focus` as the listener since `field` is undefined when first rendering -->
<button on:click={() => field.focus()}>Focus field</button> // 
```

## Other

- all block-level elements have readonly `clientWidth`, `clientHeight`, `offsetWidth`, `offsetHeight` bindings
- elements with `contenteditable="true"` support `bind:textContent` and `bind:innerHTML`
- media elements support bindings like `currentTime`, `duration`, `paused`
    - [full list here](https://svelte.dev/tutorial/media-elements)

# Slots

- declare a default location for a component's children using `<slot>`
    - put fallback content inside `<slot></slot>`
- to use multiple slots, give each `<slot>` a `name` attribute, and in the parent component add `slot="name"` to the content
- you can declare multiple slots using `name` attributes, and provide content by adding `slot` attributes as children

```html
<!-- in the PageHeader component -->
<hgroup>
    <slot name="heading">Heading</slot>
    <slot name="subheading">Subheading</slot>
</hgroup>

<!-- in the parent component -->
<PageHeader>
    <h1 slot="heading">Weather Report</h1>
    <p slot="subheading">June 1, 2023</p>
</PageHeader>
```

- you can check if a slot has content using `$$slots[name]`

```html
{#if $$slots.subheading}
    <slot name="subheading"></slot>
{/if}
```

## Slot Props

#todo

## Fragments

- Add elements to a slot without a wrapping element using `<svelte:fragment>`

```html
<PageHeader>
    <svelte:fragment slot="header">
        <span>This content won't be</span>
        <span>wrapped in an element</span>
    </svelte:fragment>
</PageHeader>
```

# Events

- bind event listeners using `on:event`

```html
<script>
	let count = 0

	function incrementCount() {
		count++
	}
</script>

<button on:click={incrementCount}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

- you can also declare event handlers inline using a function

```html
<button on:click={e => count++}>
    Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

- add & chain modifiers using `|`
    - preventDefault
    - stopPropagation
    - passive
    - nonpassive
    - capture
    - once
    - self
    - trusted

```html
<button on:click|once|trusted={...}>
```

## Special elements

- There are special elements for adding listeners to objects outside the component
    - `<svelte:window>`
        - also has bindings for `inner(Width|Height)`, `outer(Width|Height)`, `scroll(X|Y)`, `online` (matches window.navigator.onLine), `devicePixelRatio`
            - all except `scroll(X|Y)` are readonly
    - `<svelte:document>`
        - has readonly bindings for `fullscreenElement` and `visibilityState`
    - `<svelte:body>`
- All of these must appear at the top level
- Listeners on all of these will be cleaned up automatically when the component is destroyed, and are safe to use with SSR

## Component events

- Create an event dispatcher to fire events
    - `event.detail` holds the payload

```html
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher()

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		})
	}
</script>

<!-- to listen -->
<script>
    function handleMessage(event) {
        alert(event.detail.text)
    }
</script>
<Component on:message={handleMessage} />
```

- component events do not bubble - to forward events up from a child, use `on:event` without a handler

```html
<!-- forwards all message events from Inner to this component's parent -->
<Inner on:message />
```

- this also works for DOM events, ex. for creating a custom Button component

```html
<!-- in CustomButton.svelte -->
<button on:click>Click Me</button>

<!-- in parent component -->
<CustomButton on:click={handleClick} />
```

- `createEventDispatcher` can accept a type argument with a list of event names and parameters

```ts
createEventDispatcher<{
    click: null
    submit: string | null
}>()
```

- the component event handlers can be typed with `ComponentEvents`

```html
<script>
    import type { ComponentEvents } from 'svelte'
    type ExampleEvents = ComponentEvents<Example>

    function handleSubmit(event: ExampleEvents['submit']) {
        console.log(event.detail) // will be typed as string|null
    }
</script>

<Example on:submit={handleSubmit} />
```

# Lifecycle

```html
<script>
import { onMount } from 'svelte'
let photos = []

onMount(async () => {
    const res = await fetch('/tutorial/api/album')
    photos = await res.json()
})
</script>
```

- available functions:
    - `onMount`
{ #23473f}

        - if onMount returns a function, it will be called when the component is destroyed
    - `onDestroy`
    - `beforeUpdate` (before DOM updates)
        - first runs before the component has mounted, so test for existence of any DOM elements
    - `afterUpdate` (after DOM updates)
    - `tick` (returns a promise that resolves as soon as pending state changes have been applied)
        - can be called any time, not just during initialization
- lifecycle functions can be called from helper functions, as long as they are called during first init
- lifecycle functions don't run during SSR, except for `onDestroy`

# Stores

- stores are just objects with a `subscribe` method that notifies whenever the value changes
    - stores should be placed in standard `.js` files, not `.svelte` files
- multiple components can subscribe to the same store
- calling `subscribe` returns an `unsubscribe` function that should be called before the component is destroyed

## Auto-subscription

- if a store is imported at the top level, you can access its value using `$` to subscribe and unsubscribe automatically
    - you can also directly assign to store values using `$`

```html
<script>
    import { count } from './stores.js'

    $: console.log('The count is ' + $count)
</script>

<h1>The count is {$count}</h1>
```

## Writable

- *writable* stores also have `set` and `update` methods
    - `set` directly takes a value, `update` takes a function that gets the current value and returns a new value
    - you can use [[Development/Cheat sheets/Svelte cheat sheet#Components\|component binding]] on writable stores

```js
// stores.js
import { writable } from 'svelte/store'

export const count = writable(0)
```

```html
<!-- App.svelte -->
<script>
    import { onDestroy } from 'svelte'
    import { count } from './stores.js'
    
    let countValue
    const unsubscribe = count.subscribe(value => {
        countValue = value
    })

    // these could all be in different components
    function increment() {
        count.update(n => n + 1)
    }

    function reset() {
        count.set(0)
    }

    onDestroy(unsubscribe)
</script>
```

## Readable

- *readable* stores are used for values that shouldn't be set by components
- readable stores take an initial value, and have a `start` function which takes a `set` callback and returns a `stop` function
    - `start` is called on first subscriber, `stop` is called when the last subscriber unsubscribes in order to clean up

```js
// stores.js
import { readable } from 'svelte/store'

export const time = readable(new Date(), function start(set) {
	const interval = setInterval(() => {
		set(new Date())
	}, 1000)

	return function stop() {
		clearInterval(interval);
	}
});
```

```html
<!-- App.svelte -->
<script>
	import { time } from './stores.js'

	const formatter = new Intl.DateTimeFormat('en', {
		hour12: true,
		hour: 'numeric',
		minute: '2-digit',
		second: '2-digit'
	});
</script>

<h1>The time is {formatter.format($time)}</h1>
```

## Derived

- *derived* stores are based on other stores

```js
// stores.js, continuing from the above
import { derived } from 'svelte/store'

const start = new Date()

export const elapsed = derived(time, ($time) =>
    Math.round(($time - start) / 1000)
)
```

```html
<!-- App.svelte -->
<p>
	This page has been open for
	{$elapsed}
	{$elapsed === 1 ? 'second' : 'seconds'}
</p>
```

## Custom stores

- any object that implements `subscribe` is a store, so you can create custom stores that add their own logic and/or hide the default set and update methods

```js
function createCount() {
    const { subscribe, set, update } = writable(0)
    return {
        subscribe,
        increment: () => update(n => n + 1),
        decrement: () => update(n => n - 1),
        reset: () => set(0),
    }
}
```

## get

- Use `get` if you need to get the value of a store once, without subscribing to it
    - This subscribes, reads the value, then unsubscribes, so it should be used sparingly

```js
import { get } from 'svelte/store'

const value = get(store)
```

# SvelteKit

## Basics

> [!warning]
> If using Yarn, [[Development/Cheat sheets/NPM and Yarn cheat sheet#Disable Yarn PnP\|disable Yarn PnP]] or you may run into SvelteKit errors!

- Create a project: `npm create svelte@latest project-name`
    - or `yarn create`
- Store assets in `src/lib` and import them using `$lib`

## Pages

- Pages are stored in `src/routes/{page name}/+page.svelte`
- `src/routes/+page.svelte` is the index page
- To disable SSR for a page, add `export const ssr = false` to `+page.(js|ts)`
- Use the `page` store to access data like the URL, route ID, and slug params

```html
<script>
import { page } from '$app/stores'

const searchQuery = $page.url.searchParams.get('query')
</script>
```

- Use `$app/environment` to run code only in certain situations (ex. only accessing `localStorage` in the browser)

```js
import { browser, dev, building } from '$app/environment'

$: {
    if (browser) localStorage.setItem('clickCount', clickCount)
    if (dev) console.log({ clickCount })
    if (building) console.log('is prerendering')
}
```

## Data loading

- Create a `+page.js` or `+page.ts` file next to `+page.svelte`
    - This will run on both client and server - to make it server only, name it `+page.server.(js|ts)`
- Export a `load` function that returns your data
- Define a `data` prop inside `+page.svelte` to receive the data

```js
// +page.js
import data from '$lib/assets/data.json'

export function load() {
    return {
        posts: data.posts,
        comments: data.comments,
    }
}
```

```html
<!-- +page.svelte -->
<script>
export let data

const posts = data.posts
</script>
```

## Layouts

- `+layout.svelte` will apply to every page, and should contain a `<slot>` for page content
    - Create a `+layout.svelte` in a folder to apply it to every page in that folder and subfolders
- Use layouts to add nav, import global stylesheets, etc
- Layouts can have a `+layout.(js|ts)` file for [[Development/Cheat sheets/Svelte cheat sheet#Data loading\|loading data]], which is merged with page-specific data
- Layouts can access data from their children using `$page.data`

## SCSS and PicoCSS

- Install dependencies

```shell
npx svelte-add@latest scss
```

```shell
yarn add @picocss/pico
```

```shell
yarn
```

- Add `id="root"` to the `<div>` container in `app.html`

```html
<body data-sveltekit-preload-data="hover">
    <div id="root" style="display: contents">%sveltekit.body%</div>
</body>
```

- Add this to `src/app.scss`

```scss
@use '@picocss/pico/scss/pico' with (
  $semantic-root-element: '#root',
  $enable-semantic-container: true
);
```

### Cascade layer

- To load Pico on a cascade layer, add the code above to `src/pico.scss`, and add this to `app.scss` instead

```scss
@use 'sass:meta';

@layer pico {
	@include meta.load-css('pico');
}
```

- Anything you write in `pico.scss` will be on the `pico` layer, and will be overridden by anything in `app.scss` or components

### See also

- [[Development/PicoCSS snippets\|PicoCSS snippets]]
