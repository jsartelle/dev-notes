---
{"dg-publish":true,"dg-path":"Notes/Svelte.md","permalink":"/notes/svelte/","tags":["language/svelte"]}
---


# Markup

- HTML markup goes in the root level of a `.svelte` file
    - elements follow the same self-closing rules as standard HTML (ex. `<div />` is incorrect)
- JS code goes in the `<script>` tag
    - use `<script lang="ts">` for TypeScript
    - all variables in the `<script>` block can be used in the markup
    - exported values can be imported in other files as normal
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
<img src={src} alt="Photo of {name}">
```

- if an attribute name and value are the same, you can leave out the name as a shorthand (**don't forget the curly braces**)

```html
<img {src} alt="Example">
```

- objects can be spread onto components and elements

```html
<PackageInfo {...pkg} />
```

## Flow control

### if

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

### foreach

- use `{#each iterable as value, index (key)}` to loop over iterables
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

### await

- await promises directly in markup using `{#await promise}`

```html
{#await promise}
	<p>...waiting</p>
{:then number}
	<p>The number is {number}</p>
{:catch error}
	<p style="color: red">{error.message}</p>
{/await}
```

- to show nothing until the promise resolves, use `{#await promise then value}`

```html
{#await promise then number}
    <p>The number is {number}</p>
{/await}
```

## Snippets and children

- allow you to store blocks of markup in variables, somewhat similar to [[Development/Notes/React#JSX\|JSX]]
- snippets can take arguments, and can be passed as [[Development/Notes/Svelte#Props\|props]]

```html
{#snippet monkey(emoji, description)}
    <tr>
        <td>{emoji}</td>
        <td>{description}</td>
        <td>\u{emoji.charCodeAt(0).toString(16)}\u{emoji.charCodeAt(1).toString(16)}</td>
        <td>&amp#{emoji.codePointAt(0)}</td>
    </tr>
{/snippet}

{@render monkey('🙈', 'see no evil')}
{@render monkey('🙉', 'hear no evil')}
{@render monkey('🙊', 'speak no evil')}

<CustomTable rowFunction={monkey} />
```

- snippets declared inside a component's tag automatically become a prop on that component
    - content inside a component's tag that isn't in a snippet becomes part of the `children` prop

```html
<FilteredList data={colors} field="name">
	<header>
		<span class="color"></span>
		<span class="name">name</span>
		<span class="hex">hex</span>
		<span class="rgb">rgb</span>
		<span class="hsl">hsl</span>
	</header>

	{#snippet row(d)}
		<div class="row">
			<span class="color" style="background-color: {d.hex}"></span>
			<span class="name">{d.name}</span>
			<span class="hex">{d.hex}</span>
			<span class="rgb">{d.rgb}</span>
			<span class="hsl">{d.hsl}</span>
		</div>
	{/snippet}
</FilteredList>
```

```html
<!-- FilteredList.svelte -->
<script>
    import data from './data'
    let { children, row } = $props()
</script>

<div class="header">
    {@render children()}
</div>

<div class="content">
    {#each data as d}
        {@render row(d)}
    {/each}
</div>
```

## Special tags

- render HTML strings using `{@html string}`

```html
<script>
    const string = 'Here is some <strong>HTML</strong>'
</script>

<p>{@html string}</p>
```

- `{@debug var1, var2, ...}` will log the values of each variable when they change, and pause execution if devtools are open
    - use `{@debug}` without arguments to pause when *any* state changes

- `{@const assignment}` creates a local constant

```html
{#each boxes as box}
    {@const area = box.width * box.height}
    {box.width} * {box.height} = {area}
{/each}
```

## Special elements

- `<svelte:element this={tagName}>` lets you render different elements based on a string tag name
    - if `this` is falsy, nothing will be rendered

- `<svelte:head>` lets you insert elements into the `<head>`

- Elements for adding event listeners to objects outside the component:
    - `<svelte:window>`
        - also has bindings for `inner(Width|Height)`, `outer(Width|Height)`, `scroll(X|Y)`, `online` (matches window.navigator.onLine)
            - all except `scroll(X|Y)` are readonly
    - `<svelte:document>` and `<svelte:body>`
        - for `mouseenter` and `mouseleave` use `<svelte:body>`
        - allows you to use [[Development/Notes/Svelte#Actions\|#Actions]] on these elements
    - All of these must appear at the top level
    - Listeners on all of these will be cleaned up automatically when the component is destroyed, and are safe to use with SSR

## `＜script module＞`

- `<script>` blocks with the `module` attribute will run once per module, not once per component
- the rest of the component has access to variables in the `<script module>` block, but not vice versa
    - variables in `<script module>` can be exported and used in other files
- example: an audio player component that pauses if another instance of the same component starts playing

```html
<script module>
	let current;
</script>

<audio onplay={(e) => {
    if (e.currentTarget !== current) {
        current?.pause()
        current = e.currentTarget
    }
}}
```

# Styling and animation

- styles go in the `<style>` tag, and are scoped to the component
    - scoped selectors take precedence over identical global selectors
- make selectors global using `:global()`
    - can wrap parts of selectors, ex. `.foo :global(.bar)`
- you can also create a global block:

```css
:global {
    .foo { }
    .bar { }
}
```

- keyframes are scoped by default
    - to create global keyframes, prefix the name with `-global-`, but reference it in rules without the prefix

```css
@keyframes -global-spin { ... }

.square {
    animation: 1s spin;
}
```

## Classes

- classes can be added with a string as normal, or conditionally using array or object syntax (the same as the `classnames` package)
- in the example, the `card` class is always applied, and the `selected` and `flipped` classes are applied if the state values of the same name are truthy

```jsx
<button class={["card", selected && "selected", { flipped }]} />
```

## Styles

- inline styles can be set individually with `style:propName`, including custom properties
    - mark them as important by adding `|important` to the name
    - you can also use `style="foo: bar"` as normal, and mix and match

```html
<script>
    const bgOpacity = 0.5
    const color = blue
</script>

<p style:color style:--opacity={bgOpacity}></p>
```

## Custom properties

- custom properties can be set on components without using `style:`
    - this wraps the component in an element with `display: contents`, which may affect global CSS selectors such as `.parent > .child`

```html
// Box.svelte
<div class="box"></div>

<style>
    .box {
        background-color: var(--color, #ddd);
    }
</style>
```

```html
// App.svelte
<script>
    const color = $state('blue')
</script>

<Box --color={color} />
```

## Transitions

- apply transitions from `svelte/transition` when an element is added or removed

```html
<script>
	import { fade } from 'svelte/transition';

	let visible = $state(true);
</script>

{#if visible}
	<p transition:fade>
		Fades in and out
	</p>
{/if}
```

- transitions can accept parameters, and can reverse mid-transition if the element is toggled while a transition is playing

```html
<script>
	import { fly } from 'svelte/transition';

	let visible = $state(true);
</script>

{#if visible}
	<p transition:fly={{ y: 200, duration: 2000 }}>
		Flies in and out
	</p>
{/if}

```

- add `|global` to the transition name to make it play when any parent is added or removed, not just the element with the transition itself

- transitions fire `onintrostart`, `onintroend`, `onoutrostart`, `onoutroend` events

- use `{#key expression}` to destroy and recreate the element each time `expression` changes, which will replay transitions
    - in the example below, the typewriter transition will replay whenever the displayed message changes

```html
{#key i}
	<p in:typewriter={{ speed: 10 }}>
		{messages[i] || ''}
	</p>
{/key}
```

- you can also use `in` and `out` to apply separate transitions on entry or leave, and these are not reversible

```html
{#if visible}
	<p in:fly={{ y: 200, duration: 2000 }} out:fade>
		Flies in, fades out
	</p>
{/if}
```

- transitions are written in JavaScript - see [Transitions / Custom CSS transitions • Svelte Tutorial](https://svelte.dev/tutorial/svelte/custom-css-transitions)

## Animate moving elements in the DOM

- use the `crossfade` function to link an element being removed from one place in the DOM to an element being added in another place using a key, and animate between the two
- use the `animate:` directive to apply FLIP animations to the surrounding elements (must be in a keyed each block)
- example: [Advanced transitions / Animations • Svelte Tutorial](https://svelte.dev/tutorial/svelte/animations)

## Tweening

- use the `Tween` class to tween values, along with easing functions from `svelte/easing`
- has a writable `target` property and a read-only `current` property

```html
<script>
    import { Tween } from 'svelte/motion'
    import { cubicOut } from 'svelte/easing'

    let progress = new Tween(0, {
        duration: 400,
        easing: cubicOut
    })
</script>

<progress value={progress.current}></progress>

<button onclick={() => (progress.target += 0.1)}>
	Progress
</button>
```

## Springs

- `Spring` is good for values that change frequently
- in the example, `coords` updates to follow the mouse cursor, but with a spring effect

```html
<script>
	import { Spring } from 'svelte/motion';

	let coords = new Spring({ x: 50, y: 50 }, {
		stiffness: 0.1,
		damping: 0.25
	});

	let size = new Spring(10);
</script>

<svg
	onmousemove={(e) => {
		coords.target = { x: e.clientX, y: e.clientY };
	}}
	onmousedown={() => (size.target = 30)}
	onmouseup={() => (size.target = 10)}
	role="presentation"
>
	<circle
		cx={coords.current.x}
		cy={coords.current.y}
		r={size.current}
	/>
</svg>

```

## Preprocessors

- Vite supports PostCSS, SCSS, Less, Stylus, and SugarSS (already set up in SvelteKit)
    - may be prompted to install dependencies

```js
// svelte.config.js
import { vitePreprocess } from '@sveltejs/vite-plugin-svelte';

export default {
  preprocess: [vitePreprocess()]
};
```

# State and reactivity

- declare reactive variables with the `$state` rune
- state is deeply reactive, and can be written to or modified directly
    - declare state with `let` to allow writing
- state can be placed inside any file named with `.svelte.js`, and shared between components like a store
- `$state.snapshot` can be used to take a static snapshot of a state value, useful for passing to third-party libraries

```js
let numbers = $state([1, 2, 3, 4])

function addNumber() {
    numbers.push(numbers.length + 1)
}
```

- state can be TypeScript typed like a normal variable

## Reactive builtins

- Svelte includes reactive versions of `Map`, `Set`, `Date`, `URL` and `URLSearchParams` in `svelte/reactivity`

```js
<script>
	import { SvelteDate } from 'svelte/reactivity';

	let date = new SvelteDate();

	const pad = (n) => n < 10 ? '0' + n : n;

	$effect(() => {
		const interval = setInterval(() => {
			date.setTime(Date.now());
		}, 1000);

		return () => {
			clearInterval(interval);
		};
	});
</script>

<p>The time is {date.getHours()}:{pad(date.getMinutes())}:{pad(date.getSeconds())}</p>

```

## Class properties

- state can be used to make class properties reactive, and you can use getters and setters to validate the state

```js
class Box {
    #width = $state(0);
    #height = $state(0);

    constructor(width, height) {
        this.#width = width;
        this.#height = height;
    }

    get width() {
        return this.#width;
    }

    get height() {
        return this.#height;
    }

    set width(value) {
        this.#width = Math.max(0, Math.min(MAX_SIZE, value));
    }

    set height(value) {
        this.#height = Math.max(0, Math.min(MAX_SIZE, value));
    }
```

## Derived state

- use `$derived` to compute state from other state
    - `$derived` accepts expressions, `$derived.by` accepts functions
- derived state is read-only

```js
let numbers = $state([1, 2, 3, 4])
let total = $derived(numbers.reduce((t, n) => t+n, 0))
```

## Raw state

- use `$state.raw()` to create state that isn't deeply reactive, useful if the state is only needed during rendering
- raw state can't be mutated, but it can be reassigned
- in the example below, the `<polyline>` will be updated whenever `data` changes, but since we never look at the individual `data` values we dont' need them to be reactive

```html
<script>
import { poll } from './data.js';
let data = $state.raw(poll());
</script>

<polyline points={data.map((d, i) => [x(i), y(d)]).join(' ')} />
```

## Logging state

- use the `$inspect` rune to log state whenever it changes
    - will automatically be stripped out of production builds
    - pass a logging function to `.with()` to change how the value is logged

```js
$inspect(numbers).with(console.trace)
```

# Effects

- use `$effect` to re-run a function when any of the state it uses changes
    - prefer using `$derived` or [[Development/Notes/Svelte#Events\|event listeners]] when possible
- return a cleanup function to run before the effect re-runs or the component is destroyed
- effects don't run during server-side rendering
- `$effect.pre` runs before the DOM updates - if checking DOM elements, make sure they exist first!

```js
let elapsed = $state(0)
let interval = $state(1000)

$effect(() => {
    const id = setInterval(() => elapsed += 1, interval)
    return () => clearInterval(id)
})
```

## untrack

- use `untrack` to exclude state inside `$derived` or `$effect` from being treated as a dependency

```js
$effect(() => {
	// this will run when `data` changes, but not when `time` changes
	save(data, {
		timestamp: untrack(() => time)
	});
});
```

# Components

- import components within the `<script>` tag
- component names are always capitalized
- can be self-closing
- **don't** need to have a single root element

```html
<script>
    import Component from './Component.svelte'
</script>

<Component />
```

- components use the `Component` TypeScript type
- component props can be extracted with `ComponentProps<Component>`

# Props

- destructured from the `$props` rune
- can be given default values, renamed, and have rest properties, just like standard destructuring

```js
let { answer = 42, renamed: newName, ...rest } = $props()
```

- the child component can temporarily *reassign* a prop, and it will keep the new value until the prop is updated by the parent - useful for ephemeral state
    - however, props should not be *mutated* (use [[Development/Notes/Svelte#Bindings\|$bindable]] instead)

- props can be TypeScript typed like any object
    - to use generics, add a `generics` attribute to the `script` tag

```html
<script lang="ts" generics="Item extends { text: string }">
	interface Props {
		items: Item[];
		select(item: Item): void;
	}

	let { items, select }: Props = $props();
</script>
```

- HTML element types can be imported from `svelte/elements `
    - if an element doesn't have its own type definitions use `SvelteHTMLElements`

```html
<script lang="ts">
	import type { SvelteHTMLElements } from 'svelte/elements';

	let { children, ...rest }: SvelteHTMLElements['div'] = $props();
</script>

<div {...rest}>
	{@render children?.()}
</div>
```

# Context

- lets you pass any data (including state) down the tree using a key
    - the key can be any value (not just a string), which lets you control who can access the context

```js
// parent component
import { setContext } from 'svelte'

setContext('canvas', { addItem })

function addItem(fn) {
	$effect(() => {
		items.add(fn);
		return () => items.delete(fn);
	});
}
```

```js
// child component
import { getContext } from 'svelte'

const { addItem } = getContext('canvas')
```

# Events

- bind event listeners using `on<name>`, just like standard HTML

```html
<script>
	let count = 0

	function incrementCount() {
		count++
	}
</script>

<button onclick={incrementCount}>
	Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

- you can also declare listeners inline using a function

```html
<button onclick={e => count++}>
    Clicked {count} {count === 1 ? 'time' : 'times'}
</button>
```

- make a handler capturing (instead of bubbling) by adding `capture` to the name - ex. `onclickcapture`

## Component events

- pass event listeners to components as props

```html
// Stepper.svelte
<script>
    let { increment, decrement } = $props()
</script>

<button onclick={decrement}>-1</button>
<button onclick={increment}>+1</button>
```

```html
// App.svelte
<script>
    import Stepper from './Stepper.svelte'
    let value = $state(0)
</script>

<Stepper
     increment={() => value += 1}
     decrement={() => value -= 1}
/>
```

# Bindings

## Form elements (controlled components)

- declare two-way bindings for `<input>` and `<textarea>` values using `bind:value={var}`
    - if the variable name is also `value` you can just use `bind:value`

```html
<script>
	let name = 'world'
</script>

<input bind:value={name}>

<h1>Hello {name}!</h1>
```

- you can use `bind:value` with `<select>` elements, and use objects as `<option>` values
    - if the `multiple` attribute is used, selected values will be collected into an array

```html
<script>
    let questions = $state([
		{
			id: 1,
			text: `Where did you go to school?`
		},
		{
			id: 2,
			text: `What is your mother's name?`
		},
		{
			id: 3,
			text: `What is another personal fact that an attacker could easily find with Google?`
		}
	]);

	let selected = $state();

	let answer = $state('');
</script>

<form onsubmit={handleSubmit}>
	<select
		bind:value={selected}
		onchange={() => (answer = '')}
	>
		{#each questions as question}
			<option value={question}>
				{question.text}
			</option>
		{/each}
	</select>
</form>
```

- for checkboxes use `bind:checked`
- for radio buttons and grouped checkboxes, use `bind:group`
    - checkbox values will be collected into an array

```html
<script>
	let scoops = $state(1);
	let flavours = $state([]);
</script>

{#each [1, 2, 3] as number}
	<label>
		<input
			type="radio"
			name="scoops"
			value={number}
			bind:group={scoops}
		/>

		{number} {number === 1 ? 'scoop' : 'scoops'}
	</label>
{/each}

{#each ['cookies and cream', 'mint choc chip', 'raspberry ripple'] as flavour}
	<label>
		<input
			type="checkbox"
			name="flavours"
			value={flavour}
			bind:group={flavours}
		/>

		{flavour}
	</label>
{/each}
```

## Binding props

- you can bind to a prop of a child component, if the child marks it as bindable by setting it equal to `$bindable`
- `$bindable` can take a fallback value, which is used if the prop is not bound

```html
<!-- App.svelte -->
<script>
    let pin = $state('1234')
</script>

<Keypad bind:value={pin} />
```

```html
<!-- Keypad.svelte -->
<script>
    let { value = $bindable('0000') } = $props()
</script>
```

## this (refs)

- the `this` binding lets you get a readonly binding to an element or component, similar to refs in Vue or React
    - the value is undefined until the component has mounted, so you should only work with the elements inside effects or event listeners
- refs on components can access any exported data

```html
<script>
    import InputField from './InputField.svelte';

    let canvas
    let field

    $effect(() => {
        const ctx = canvas.getContext('2d')
        // do canvas-y stuff
    });
</script>

<canvas bind:this={canvas}></canvas>

<!-- assuming InputField exports a `focus` method -->
<InputField bind:this={field} />

<!-- you can't simply pass `field.focus` as the listener since `field` is undefined on first render -->
<button onclick={() => field.focus()}>Focus field</button> // 
```

## Other

- elements have readonly `clientWidth`, `clientHeight`, `offsetWidth`, `offsetHeight` bindings
    - inline elements without intrinsic dimensions can't be observed unless their `display` value is changed (see [[Development/Notes/JavaScript#ResizeObserver\|ResizeObserver]])
- elements with `contenteditable="true"` support `bind:textContent` and `bind:innerHTML`
- media elements support bindings like `currentTime`, `duration`, `paused`

# Actions

- actions are functions that are called when an element is created, and can do things like add event listeners, or hook up third-party libraries to elements
- typically use [[Development/Notes/Svelte#Effects\|$effect]] so they are cleaned up when the element unmounts
- not called during SSR
- actions can receive an argument, but will not re-run if the argument changes
    - however, [[Development/Notes/Svelte#State and Reactivity\|state]] can be passed via a function, and [[Development/Notes/Svelte#Effects\|effects]] will re-run when that state changes
- applied to elements with `use:`

```html
<script>
	import tippy from 'tippy.js';

	let content = $state('Hello!');

	function tooltip(node, fn) {
		$effect(() => {
			const tooltip = tippy(node, fn());

			return tooltip.destroy;
		});
	}
</script>

<button use:tooltip={() => ({ content })}>
	Hover me
</button>
```

# Lifecycle hooks

- `onMount`
    - if onMount returns a function synchronously, it will be called when the component is unmounted
    - does *not* run on the server
- `onDestroy`
    - *does* run on the server
- these must be called during initialization (not within effects or the like), but can be called from external modules

```js
import { onMount } from 'svelte';

onMount(() => {
    const interval = setInterval(() => {
        console.log('beep');
    }, 1000);

    return () => clearInterval(interval);
});
```

- `tick`: returns a promise that resolves when pending state changes have been applied
    - can be used inside `$effect.pre` to continue once the UI has updated

```js
import { tick } from 'svelte';

$effect.pre(() => {
    console.log('the component is about to update');
    tick().then(() => {
        console.log('the component just updated');
    });
});
```

# Error boundaries

- use `<svelte:boundary>` to capture errors
- can take an `onerror` handler and/or a `failed` [[Development/Notes/Svelte#Snippets and children\|snippet]], both of which receive the error and a reset function as arguments

```html
<svelte:boundary onerror={(e, reset) => console.error(e)}>>
    <FlakyComponent />

    {#snippet failed(error, reset)}
		<p>Oops! {error.message}</p>
		<button onclick={reset}>Reset</button>
	{/snippet}
</svelte:boundary>
```

# SvelteKit

## Basics

> [!info] Create a new project:
>
> ```bash
> npx sv create my-app
> ```

- shared components and code go in `src/lib`, and are imported from `$lib`
    - server-only code can be placed in `src/lib/server` and imported from `$lib/server`

## Pages and routing

- pages are stored in `src/routes/{page name}/+page.svelte`
    - `src/routes/+page.svelte` is the index page
- create dynamic route parameters by adding square brackets to the folder (ex. `/src/routes/blog/[slug]/+page.svelte`)
    - advanced routing: [Advanced routing / Optional parameters • Svelte Tutorial](https://svelte.dev/tutorial/kit/optional-params)
- programmatic routing functions and callbacks are in `$app/navigation`

## Layouts

- `+layout.svelte` applies to every sibling and child page, and receives the page content as the [[Development/Notes/Svelte#Snippets and children\|children prop]]
- use layouts to add nav, import shared stylesheets, etc
- you can "break out" of a nested layout by adding a parent segment to "reset" to - ex. `+page@b.svelte` will render layouts as if the page was in the `b` folder
    - `+page@.svelte` would only use the root layout

## Page and navigation state

- layouts and pages can import these read-only state objects from `$app/state`:
    - `page`:
        - `url` — the URL of the current page
        - `params` — the current page’s parameters
        - `route` — an object with an id property representing the current route
        - `status` — the HTTP status code of the current page
        - `error` — the error object of the current page, if any (you’ll learn more about error handling in later exercises)
        - `data` — the data for the current page, combining the return values of all load functions
        - `form` — the data returned from a form action
    - `navigating`:
        - `from` and `to`:
            - `params`
            - `route`
            - `url`
            - can be used to show a loader (if `navigating.to` has a value)
        - `type`: `link`, `popstate`, `goto`

## Preloading

- preload pages by adding the `data-sveltekit-preload-data` attribute to a link (`<a>`), or any element containing links
    - this will run the `load` functions in `page.js` and `layout.js`
    - the default template has this on the body, so all links are preloaded
    - accepts these values:
        - `hover`: start preloading when the link is hovered
        - `tap`: start preloading when the link is tapped
        - `off`: don't preload (override higher level attributes)
- `data-sveltekit-preload-code` preloads the page's JavaScript, but not the data
    - `eager` preloads the whole page as soon as navigation finishes
    - `viewport` preloads what's in the viewport
    - `hover`, `tap`, `off` behave the same as above
- you can also use the `preloadData` and `preloadCode` functions in your script

## Data fetching

- create a `+page.js` or `+page.server.js` file next to `+page.svelte`
    - `+page.js` runs on server and client, `+page.server.js` runs only on the server
    - you can also use both, and `+page.js` can access the data from `+page.server.js` in the `data` property
        - the data isn't merged, so `+page.js` must return anything from the server's data that should be accessible to the client
- export a `load` function that returns your data
    - receives an object with the route params, and functions for setting headers and cookies
        - make sure to specify a path when setting cookies
    - deeper load functions can access data from their parents using `await parent()`
- use a `data` prop in the page to receive the data
- `+layout.js` (or `+layout.server.js`) is the same, but runs for every sibling and child layout, and data is merged with the data from `page.js`
    - data can be accessed from the layout or page files
- use the `redirect` function from `@sveltejs/kit` to redirect
- can export page options, which can be overridden further down the tree
    - `ssr`: enable/disable SSR for a page or group of pages
    - `prerender`: set to `true` to render the page at build time instead of per-request

```js
// +page.server.js
import { error } from '@sveltejs/kit';
import { posts } from '../data.js';

export function load({ params, setHeaders, cookies }) {
	const post = posts.find((post) => post.slug === params.slug);

	if (!post) error(404);

    const visited = cookies.get('visited');
	cookies.set('visited', 'true', { path: '/' });

    setHeaders({
		'Cache-Control': 'no-store'
	});

	return {
		post
	};
}

```

```html
<!-- +page.svelte -->
<script>
let { data } = $props()

const posts = data.posts
</script>
```

## Form actions

- the `+page.js` (or `+page.server.js`) file can export actions which can handle receiving form data
- you can have a single `default` action, *or* multiple named actions
    - if there is only one action you can omit the `action` prop on the `<form>` element
    - forms can use actions defined on other pages - ex. `action="/todos?/create"`
- actions can return data which is accessible in the `form` prop
    - the `fail` function can be used to return an error (also accessible in the `form` prop)
- form actions work without JavaScript, but the `use:enhance` directive on the form avoids reloading the page if JavaScript is enabled (which allows adding [[Development/Notes/Svelte#Transitions\|transitions]])
    - `use:enhance` can take a function to update state in order to show a message while the action is happening

```js
// +page.js
import { fail } from '@sveltejs/kit';

export const actions = {
    create: async ({ cookies, request }) => {
		const data = await request.formData();

		try {
			db.createTodo(cookies.get('userid'), data.get('description'));
		} catch (error) {
			return fail(422, {
				description: data.get('description'),
				error: error.message
			});
		}
	},

	delete: async ({ cookies, request }) => {
		const data = await request.formData();
		db.deleteTodo(cookies.get('userid'), data.get('id'));
	}
};
```

```html
<!-- +page.svelte -->
<script>
    import { fly, slide } from 'svelte/transition';
	import { enhance } from '$app/forms';

	let { data, form } = $props();

	let creating = $state(false);
	let deleting = $state([]);
</script>

<div class="centered">
	<h1>todos</h1>

	{#if form?.error}
		<p class="error">{form.error}</p>
	{/if}

	<form
		method="POST"
		action="?/create"
		use:enhance={() => {
			creating = true;

			return async ({ update }) => {
				await update();
				creating = false;
			};
		}}
	>
		<label>
			add a todo:
			<input
				disabled={creating}
				name="description"
				value={form?.description ?? ''}
				autocomplete="off"
				required
			/>
		</label>
	</form>

	<ul class="todos">
		{#each data.todos.filter((todo) => !deleting.includes(todo.id)) as todo (todo.id)}
			<li in:fly={{ y: 20 }} out:slide>
				<form
					method="POST"
					action="?/delete"
					use:enhance={() => {
						deleting = [...deleting, todo.id];
						return async ({ update }) => {
							await update();
							deleting = deleting.filter((id) => id !== todo.id);
						};
					}}
				>
					<input type="hidden" name="id" value={todo.id} />
					<span>{todo.description}</span>
					<button aria-label="Mark as complete"></button>
				</form>
			</li>
		{/each}
	</ul>

	{#if creating}
		<span class="saving">saving...</span>
	{/if}
</div>
```

## API routes

- a `+server.js` file next to your page can export functions corresponding to HTTP methods
    - `request` is a standard `Request` object
    - must return a `Response` object
        - use the `json` function to respond with JSON
        - if you don't need to return anything, return `new Response(null, { status: 204 })`
- prefer [[Development/Notes/Svelte#Form actions\|#Form actions]] when possible for mutating data, since they can work without JavaScript
- you can update the `data` prop after mutating data, but it's not deeply reactive, so replace it
    - only update it in such a way that you would get the same result by reloading the page

```js
// todo/+server.js
import { json } from '@sveltejs/kit';

export async function POST({ request, cookies }) {
	const { description } = await request.json();

	const userid = cookies.get('userid');
	const { id } = await database.createTodo({ userid, description });

	return json({ id }, { status: 201 });
}
```

```html
<!-- +page.svelte -->
<script>
    let { data } = $props()
</script>

<label>
    add a todo:
    <input
        type="text"
        autocomplete="off"
        onkeydown={async (e) => {
            if (e.key !== 'Enter') return;

            const input = e.currentTarget;
            const description = input.value;
            
            const response = await fetch('/todo', {
                method: 'POST',
                body: JSON.stringify({ description }),
                headers: {
                    'Content-Type': 'application/json'
                }
            });

            const { id } = await response.json();

            const todos = [...data.todos, {
                id,
                description
            }];

            data = { ...data, todos };

            input.value = '';
        }}
    />
</label>
```

## Error handling

- *expected* errors are ones thrown from the `error` helper in `@sveltejs/kit`, and are shown to users
- any other errors are considered *unexpected*, and the error info is redacted
- create a `+error.svelte` component to show an error page
    - will be rendered inside the layout
    - like layouts, different parts of the route hierarchy can have their own error pages, but they aren't nested
- `src/error.html` is used if an error happens while loading the root layout or rendering an error page
    - can take a couple variables - `%sveltekit.status%` and `%sveltekit.error.message%`

## Environment variables

- can use `.env`, `.env.local` (ignored by git), `.env.[mode]` (only loaded in that mode)
- import static (replaced at build time) variables from `$env/static/private`, or dynamic (read at runtime) from `$env/dynamic/private`
- variables can only be loaded on the server unless their name begins with `PUBLIC_`, and they are imported from `/public` instead

# Svelte 4

## Special elements

- `<svelte:component>` lets an element render different components based on the `this` prop
    - `this` should be a component constructor
    - if `this` is falsy, nothing will be rendered

```html
<!-- Svelte 4 -->
<svelte:component this={CurrentComponent} />
```

- in Svelte 5, a component will re-render if the value of its constructor changes (similar to React)

```html
<!-- Svelte 5 -->
<script>
    /* ... */

    let condition = $state(false)
    let CurrentComponent = $derived(condition ? ComponentA : ComponentB)
</script>

<CurrentComponent />
```

- `<svelte:self>` lets a component contain itself (since it can't import itself)
    - in Svelte 5 components can import themselves

## Styling

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

## Reactivity

- declare reactive statements (which re-run when the values they depend on change) using a `$:` label
    - somewhat like [[Development/Notes/React#useEffect\|useEffect]] in React

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

### Updating arrays & objects

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

## Props

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

## Events

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

- you can also declare event listeners inline using a function

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

### Component events

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

- component events do not bubble - to forward events up from a child, use `on:event` without a listener

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

- the component event listeners can be typed with `ComponentEvents`

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

## Slots

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
{#if $slots.subheading}
    <slot name="subheading"></slot>
{/if}
```

### Fragments

- Add elements to a slot without a wrapping element using `<svelte:fragment>`

```html
<PageHeader>
    <svelte:fragment slot="header">
        <span>This content won't be</span>
        <span>wrapped in an element</span>
    </svelte:fragment>
</PageHeader>
```

## Stores

- stores are just objects with a `subscribe` method that notifies whenever the value changes
    - stores should be placed in standard `.js` files, not `.svelte` files
- multiple components can subscribe to the same store
- calling `subscribe` returns an `unsubscribe` function that should be called before the component is destroyed

### Auto-subscription

- if a store is imported at the top level, you can access its value using `$` to subscribe and unsubscribe automatically
    - you can also directly assign to store values using `$`

```html
<script>
    import { count } from './stores.js'

    $: console.log('The count is ' + $count)
</script>

<h1>The count is {$count}</h1>
```

### Writable

- *writable* stores also have `set` and `update` methods
    - `set` directly takes a value, `update` takes a function that gets the current value and returns a new value
    - you can use [[Development/Notes/Svelte#Components\|component binding]] on writable stores

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

### Readable

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

### Derived

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

### Custom stores

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

### get

- Use `get` if you need to get the value of a store once, without subscribing to it
    - This subscribes, reads the value, then unsubscribes, so it should be used sparingly

```js
import { get } from 'svelte/store'

const value = get(store)
```

## Lifecycle hooks

- `beforeUpdate` (before DOM updates)
    - first runs before the component has mounted, so test for existence of any DOM elements
    - in Svelte 5 use `$effect.pre` instead
- `afterUpdate` (after DOM updates)
    - in Svelte 5 use `$effect` instead
