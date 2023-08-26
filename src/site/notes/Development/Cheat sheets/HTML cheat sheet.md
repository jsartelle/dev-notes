---
{"dg-publish":true,"dg-path":"Cheat sheets/HTML cheat sheet.md","permalink":"/cheat-sheets/html-cheat-sheet/"}
---


# Elements

## `＜dialog＞`

- makes it easy to create modals
- call `.show()` to show non-modally, or `.showModal()` to show modally
- to close, either call `.close()`, or submit a form inside the modal with `method="dialog"`
    - <kbd>Esc</kbd> will close modal dialogs by default
- set the `autofocus` attribute on a child to focus it when the dialog opens
- style a modal dialog's backdrop using the `::backdrop` pseudo-element

### Animating dialogs

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

## `＜details＞` and `＜summary＞`

- Use the `open` attribute to open and close

<details style="padding: 10px; border: 1px solid currentColor;">
    <summary>Click to open</summary>
    <p>This text is inside the details element</p>
</details>

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

- Each page should only have one `<h1>` which describes the entire page.
- Do not skip heading levels (ex. don't nest `<h3>` inside `<h1>` without an `<h2>` in between)
- Use `aria-labelledby` on sectioning elements ([`<article>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/article), [`<aside>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/aside), [`<nav>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/nav), and [`<section>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/section)) to label them with a header

```html
<article aria-labelledby="article-abc123">
    <h2 id="article-abc123">Types of Fish</h2>
</article>
```

## `＜hgroup＞`

- Group a header (`<h1>` - `<h6>`) with any number of related `<p>` elements

# Other

## Quick example images

- width / height, leave out height for a square image

```
https://picsum.photos/200/300
```

- when using the same link multiple times on a page you may get the same image, to work around this you can add a seed

```
https://picsum.photos/seed/seedGoesHere/200/300
```
## img alt vs. title attributes

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

## input vs. change events

- `input` events fire every time the value of a `<input>`, `<select>`, or `<textarea>` element changes
- `change` events only fire when the change is committed by the user, or the element loses focus
    - radio buttons do not fire `change` when they are deselected
- use `change` for checkboxes & radio buttons

## DOMContentLoaded vs. load

The `load` event is fired when the whole page has loaded, including all dependent resources such as stylesheets and images. This is in contrast to `DOMContentLoaded`, which is fired as soon as the page DOM has been loaded, without waiting for resources to finish loading.

The `DOMContentLoaded` event fires when the HTML document has been completely parsed, and all deferred scripts (`<script defer src="…">` and `<script type="module">`) have downloaded and executed. It doesn't wait for other things like images, subframes, and async scripts to finish loading.

`DOMContentLoaded` does not wait for stylesheets to load, however deferred scripts *do* wait for stylesheets, and `DOMContentLoaded` queues behind deferred scripts. Also, scripts which aren't deferred or async (e.g. `<script>`) will wait for already-parsed stylesheets to load.

## Load CSS asynchronously

```html
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```

# See also

- [[Development/Clipped/Everything I Know About The Script Tag\|Everything I Know About The Script Tag]]
