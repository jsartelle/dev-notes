---
{"dg-publish":true,"dg-path":"Cheat sheets/Svelte cheat sheet.md","permalink":"/cheat-sheets/svelte-cheat-sheet/","created":"","updated":""}
---


# Markup

- JS code in a `.svelte` file goes in the `<script>` tag
- expressions in markup and attributes use single curly braces:

```js
<script>
	let name = 'world'
	let src = '/tutorial/image.gif'
</script>

<h1>
	Hello {name.toUpperCase()}!
</h1>
<img src={src} alt="Example">
```

- if an attribute name and value are the same, you can leave out the name:

```js
<img {src} alt="Example">
```

- styles go in the `<style>` tag and are *always scoped*
- to render HTML in markup:

```js
<script>
    const string = 'Here is some <strong>HTML</strong>'
</script>

<p>{@html string}</p>
```

## Logic

- conditionally render using `{#if}`, `{:else if}`, `{:else}`

```js
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

```js
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

- await promises directly in markup using `{#await promise}`
    - to show nothing until the promise resolves, use `{#await promise then value}`

```js
{#await getRandomNumber()}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

# Components

- component names are always capitalized
- import components within the `<script>` tag:

```js
<script>
    import Component from './Component.svelte'
</script>

<Component />
```

- to use a component from outside of Svelte:

```js
import App from './App.svelte'

const app = new App({
	target: document.body,
	props: {
		answer: 42
	}
});
```

## Props

- props are declared using `export`
    - assign a value to use it as the default value:

```js
// in Nested.svelte:
<script>
    export let answer = 42
</script>

// in another component:
<Nested answer={10} />
<Nested /> // answer === 42
```

- you can spread properties onto a component:

```js
<Info {...pkg} />
```

- you can access all of a component's props using `$$props`, but this is *not* recommended

# Events

- bind event listeners using `on:event`

```js
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
    - quotes are optional, but may help with syntax highlighting

```js
<button on:click="{e => count++}">
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

```js
<button on:click|once|trusted={...}>
```

## Component Events

- Create an event dispatcher to fire events
    - `event.detail` holds the payload

```js
<script>
	import { createEventDispatcher } from 'svelte';

	const dispatch = createEventDispatcher()

	function sayHello() {
		dispatch('message', {
			text: 'Hello!'
		})
	}
</script>

// to listen:
<script>
    function handleMessage(event) {
        alert(event.detail.text)
    }
</script>
<Component on:message={handleMessage} />
```

- component events do not bubble - to forward events up from a child, use `on:event` without a handler

```js
// forwards all message events from Inner to this component's parent
<Inner on:message />
```

- this also works for DOM events, ex. for creating a custom Button component:

```js
// in CustomButton.svelte
<button on:click>Click Me</button>

// in parent component
<CustomButton on:click={handleClick} />
```

# Reactivity

- declare reactive values (like computed properties) using a `$:` label:

```js
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

## Updating Arrays & Objects

- mutating arrays or objects does not trigger an update - to fix this, assign the object to itself:

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

# Bindings

## Inputs

- declare two-way input bindings using `bind:value={var}` (similar to v-model)
    - if the variable name is also `value` you can just use `bind:value`
    - also works with `<select>`, and if the `multiple` attribute is used it will create an array

```js
<script>
	let name = 'world'
</script>

<input bind:value={name}>

<h1>Hello {name}!</h1>
```

- for checkboxes use `bind:checked`
- use `bind:group` to bind multiple inputs to the same value
    - radio inputs will replace the value, checkboxes will create an array

````ad-example
collapse: closed

```js
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
	<input type=radio bind:group={scoops} name="scoops" value={1}>
	One scoop
</label>

<label>
	<input type=radio bind:group={scoops} name="scoops" value={2}>
	Two scoops
</label>

<label>
	<input type=radio bind:group={scoops} name="scoops" value={3}>
	Three scoops
</label>

<h2>Flavours</h2>

{#each menu as flavour}
	<label>
		<input type=checkbox bind:group={flavours} name="flavours" value={flavour}>
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
````

## Components

- you can bind to any prop on a component to allow two-way updating (like v-bind.sync in Vue 2, or v-model in Vue 3), but use this sparingly to avoid overcomplicating the flow of data

## this (refs)

- the `this` binding lets you get a reference to an element or component, similar to Vue refs

```js
<script>
    import InputField from './InputField.svelte';

    let canvas
    let field
</script>

<canvas bind:this={canvas}></canvas>

<InputField bind:this={field} /> // assume InputField has a `focus` method
<button on:click={() => field.focus()}>Focus field</button> // we can't put `field.focus` in the listener since `field` is undefined when first rendering
```

## Other

- all block-level elements have readonly `clientWidth`, `clientHeight`, `offsetWidth`, `offsetHeight` bindings
- elements with `contenteditable="true"` support `bind:textContent` and `bind:innerHTML`
- media elements support bindings like `currentTime`, `duration`, `paused`
    - [full list here](https://svelte.dev/tutorial/media-elements)

# Lifecycle

```js
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
        - if onMount returns a function, it will be called when the component is destroyed
    - `onDestroy`
    - `beforeUpdate` (before DOM updates)
        - first runs before the component has mounted, so test for existence of any DOM elements
    - `afterUpdate` (after DOM updates)
    - `tick` (resolves as soon as pending state changes have been applied, like Vue this.$tick)
        - can be called any time, not just during initialization
- lifecycle functions can be called from helper functions, as long as they are called during first init
- lifecycle functions don't run during SSR, except for `onDestroy`

# Stores

- stores are just objects with a `subscribe` method that notifies whenever the value changes
    - multiple components can subscribe to the same store
- calling `subscribe` returns an `unsubscribe` function that should be called before the component is destroyed
- *writable* stores also have `set` and `update` methods
    - `set` directly takes a value, `update` takes a function that gets the current value and returns a new value

```js
// stores.js
import { writable } from 'svelte/store'

export const count = writable(0)

// App.svelte
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

- *readable* stores take an initial value, and have a `start` function which takes a `set` callback and returns a `stop` function
    - `start` is called on first subscriber, `stop` is called for cleanup when the last subscriber unsubscribes

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

// App.svelte
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

- *derived* stores are based on other stores

```js
// stores.js, continuing from the above
import { derived } from 'svelte/store'

const start = new Date()

export const elapsed = derived(time, $time => {
    Math.round(($time - start) / 1000)
})

// App.svelte
```

- any object that implements `subscribe` is a store, so you can create custom stores that add their own logic and/or hide the default set and update methods:

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

## Auto-Subscription

- stores that are imported at the top level can be accessed using the $ prefix, and are unsubscribed automatically

```js
<script>
    import { count } from './stores.js'

    $: console.log('The count is ' + $count)
</script>

<h1>The count is {$count}</h1>
```

# Resources

<div class="rich-link-card-container"><a class="rich-link-card" href="https://kit.svelte.dev" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://svelte.dev/images/twitter-thumbnail.jpg')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">SvelteKit</h1>
		<p class="rich-link-card-description">
		The fastest way to build Svelte apps
		</p>
		<p class="rich-link-href">
		https://kit.svelte.dev
		</p>
	</div>
</a></div>
