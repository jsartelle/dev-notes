---
{"dg-publish":true,"dg-path":"Cheat sheets/HTML cheat sheet.md","permalink":"/cheat-sheets/html-cheat-sheet/"}
---


# Elements

## `＜a＞`

### Keyboard focus on links without href

- Links without an `href` attribute do not receive focus from the <kbd>Tab</kbd> key by default. To make them focusable add `href=""`.
    - `tabindex="0"` will not allow the link to be activated with <kbd>Enter</kbd>

## `＜datalist＞`

- holds a set of `<option>` elements to be shown as suggestions in an `<input>` element, while still letting the user enter a custom value
    - supports `<optgroup>`
- supports these `<input>` types:
    - `text`
    - `month`, `week`, `date`, `time`, `datetime-local`
    - `range`
    - `color`

<datalist id="apps">
    <option value="Obsidian"></option>
    <option value="Apple Notes"></option>
    <option value="OneNote"></option>
    <option value="Notion"></option>
</datalist>
<label>
    <span>Choose your favorite notes app:</span>
    <input type="text" list="apps" />
</label>

```html
<datalist id="apps">
    <option value="Obsidian"></option>
    <option value="Apple Notes"></option>
    <option value="OneNote"></option>
    <option value="Notion"></option>
</datalist>

<label>
    <span>Choose your favorite notes app:</span>
    <input type="text" list="apps" />
</label>
```

## `＜dialog＞` (modals)

- makes it easy to create modals
- call `.show()` to show non-modally, or `.showModal()` to show modally
- to close, either call `.close()`, or submit a form inside the modal with `method="dialog"`
    - <kbd>Esc</kbd> will close modal dialogs by default
- set the `autofocus` attribute on a child to focus it when the dialog opens
- style a modal dialog's backdrop using the `::backdrop` pseudo-element

### Animating dialogs

#todo this might need updating for `transition-behavior`

- `transition` doesn't work on modals, instead use an animation targeting the `[open]` attribute
- to animate closing (ex. for a fade out):
    - when the close button is clicked, apply a class to the dialog that triggers an animation
        - just reversing the animation that plays on open won't work, since it's still the same animation name
    - set a one-time `animationend` listener that calls `dialog.close()` when the animation ends

```js
function closeModal() {
    dialogEl.addEventListener('animationend', () => {
        dialog.close()
        dialog.classList.remove('fade-out')
    }, { once: true })
    
    dialog.classList.add('fade-out')
}
```

## `＜details＞` and `＜summary＞` (accordions)

- Use the `open` attribute to open and close

<details style="padding: 10px; border: 1px solid currentColor;">
    <summary>Click to open</summary>
    <p>This text is inside the details element</p>
</details>

### Exclusive accordions

- If multiple `<details> `elements have the same `name` attribute, only one can be open at a time - supported in Chrome and Safari as of December 2023

## `＜fieldset＞` and `＜legend＞`

- Group a set of form controls together with an optional title

<fieldset>
    <legend>Flavors</legend>
    <label>
        <input type="radio" name="flavor" value="chocolate">
        Chocolate
    </label>
    <label>
        <input type="radio" name="flavor" value="vanilla">
        Vanilla
    </label>
    <label>
        <input type="radio" name="flavor" value="strawberry">
        Strawberry
    </label>
</fieldset>

## Heading best practices

- Each page should only have one `<h1>` which describes the entire page
- Don't skip heading levels (ex. don't nest `<h3>` inside `<h1>` without an `<h2>` in between)
- Use `aria-labelledby` on sectioning elements ([`<article>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article), [`<aside>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/aside), [`<nav>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav), and [`<section>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)) to label them with a header inside

```html
<article aria-labelledby="article-abc123">
    <h2 id="article-abc123">Types of Fish</h2>
</article>
```

## `＜hgroup＞`

- Group a header (`<h1>` - `<h6>`) with any number of related `<p>` elements

## `＜img＞`

### alt vs. title attributes

The `alt` attribute is used by screen readers, and is shown if the image fails to load. It should contain text that can **replace** the image. It should not repeat information that is already provided in the text accompanying the image.

The `title` attribute is displayed in a tooltip on hover. It should contain text that **supplements** the image, like a title or caption. It works on any element, not just `img`.

```html
<img
    src="./mona-lisa.jpg"
    alt="Painting of a smiling woman, sitting with her arms folded"
    title="Mona Lisa, Leonardo da Vinci, 1503-1506"
/>
```

### Alt text for image links

If an image is also a hyperlink, the `alt` text should describe the function of the link.

## `＜script＞`

### async vs. defer

- normal scripts:
    - ==block parsing==
    - executed ==as soon as available==
- `async` scripts:
    - downloaded ==in parallel== with parsing
    - executed ==as soon as available==
    - should be used for **completely independent scripts** (not dependent on the DOM or other scripts)
        - if an async script depends on another, small script, make the other script inline and place it above the async script
- `defer` scripts:
    - downloaded ==in parallel== with parsing
    - executed ==after the document is parsed==
        - `DOMContentLoaded` will not fire until deferred scripts finish executing
    - guaranteed to ==run in the order they appear== in the document
    - scripts with `type="module"` are deferred by default
    - use for **anything that can't be async**
- `async` and `defer` are both ignored for inline scripts

# Events

## input vs. change

- `input` events fire every time the value of a `<input>`, `<select>`, or `<textarea>` element changes
- `change` events only fire when the change is committed by the user, or the element loses focus
    - radio buttons do not fire `change` when they are deselected
- ==use `change` for checkboxes & radio buttons==

## DOMContentLoaded vs. load

The `load` event is fired when the whole page has loaded, including all dependent resources such as stylesheets and images. This is in contrast to `DOMContentLoaded`, which is fired as soon as the page DOM has been loaded, without waiting for resources to finish loading.

The `DOMContentLoaded` event fires when the HTML document has been completely parsed, and all deferred scripts (`<script defer src="…">` and `<script type="module">`) have downloaded and executed. It doesn't wait for other things like images, subframes, and async scripts to finish loading.

`DOMContentLoaded` does not wait for stylesheets to load, however deferred scripts *do* wait for stylesheets, and `DOMContentLoaded` queues behind deferred scripts. Also, scripts which aren't deferred or async (e.g. `<script>`) will wait for already-parsed stylesheets to load.

# Other

## Find focused element

Use `document.activeElement` to find the currently focused element. You can add this as a live expression in the Chrome devtools to get a live updating view of the focused element.

## Example image URLs

- width / height, leave out height for a square image

```
https://picsum.photos/200/300
```

- when using the same link multiple times on a page you may get the same image, to work around this you can add a seed

```
https://picsum.photos/seed/seedGoesHere/200/300
```

## event.target vs event.currentTarget

- `event.currentTarget` is always the element that the ==event listener is attached to==
- `event.target` is the element that ==received the event==
    - this can be different if, ex. you click on a child of an element with a `click` handler
    - in the example below, if you click on the child, `target` is the child and `currentTarget` is the parent

```html
<div id="parent" onClick="handler()">
    <div id="child"></div>
</div>
```

## Load CSS asynchronously

```html
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```

# See also

- [[Development/Clipped/Everything I Know About The Script Tag\|Everything I Know About The Script Tag]]
