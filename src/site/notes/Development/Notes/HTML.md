---
{"dg-publish":true,"dg-path":"Notes/Notes/HTML.md","permalink":"/notes/notes/html/","tags":["language/html"]}
---


# Elements

## `＜a＞`

- the text inside a link should describe it (don't just say something like "click here")

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

```html
<datalist id="apps">
    <option value="Obsidian"></option>
    <option value="Apple Notes"></option>
    <option value="OneNote"></option>
    <option value="Notion"></option>
</datalist>

<label>
    <span>What is your favorite notes app?</span>
    <input type="text" list="apps" />
</label>
```

<datalist id="apps">
    <option value="Obsidian"></option>
    <option value="Apple Notes"></option>
    <option value="OneNote"></option>
    <option value="Notion"></option>
</datalist>
<label>
    <span>What is your favorite notes app?</span>
    <input type="text" list="apps" />
</label>

## `＜dialog＞` (modals/popups)

- makes it easy to create popups that can either be modal (block interaction with the rest of the page), or non-modal
- call `.show()` to show non-modally, or `.showModal()` to show modally
    - `showModal()` will render everything outside the dialog [[Development/Notes/HTML#inert\|#inert]]
- to close, either call `.close()`, or submit a form inside the modal with `method="dialog"`
    - <kbd>Esc</kbd> will close modal dialogs by default
- set the `autofocus` attribute on a child to focus it when the dialog opens
- style a modal dialog's backdrop using the `::backdrop` pseudo-element
- the `:modal` pseudo-class will target only modal dialogs (as well as fullscreen elements)
- to animate show/hide:
    - 
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/notes/notes/css/#with-transition-behavior" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## With [[Development/Notes/HTML#transition-behavior\|#transition-behavior]]

```css
dialog, [popover] {
    transition-property: opacity, display;
    transition-duration: 0.25s;

    /* when setting `display: none`, keeps the element visible until the end of the transition */
    transition-behavior: allow-discrete;

    @starting-style {
        /* triggers fade in when showing */
        opacity: 0;
    }
}

dialog:not([open]) {
        /* triggers fade out when hiding */
        opacity: 0;
    }
}

[popover]:not(:popover-open) {
        /* triggers fade out when hiding */
        opacity: 0;
    }
}
```


</div></div>


### Dismiss by clicking outside contents

- to dismiss a dialog by clicking outside the contents:
    - make the `<dialog>` element fill the screen, and remove its default styles
    - place the contents in a wrapper element
    - add a click listener to the `<dialog>` that closes it, but only if the `<dialog>` itself was clicked

```html
<dialog>
    <div>Contents go here</div>
</dialog>
```

```css
dialog {
  border: none;
  background: none;
  width: 100%;
  max-width: none;
  height: 100%;
  max-height: none;

  &::backdrop {
    all: unset;
  }

  > :first-child {
    max-width: 100%;
    max-height: 100%;
    overflow-y: auto;
  }
}
```

```js
document.querySelector('border').addEventListener('click', function (e) {
    if (e.target === this) {
        this.close()
    }
})
```

## `＜details＞` and `＜summary＞` (accordions)

- Use the `open` attribute to open and close

```html
<details name="exclusive" style="padding: 10px; border: 1px solid currentColor;">
    <summary>Click to open</summary>
    <p>This text is inside the details element</p>
</details>
```

<details name="exclusive" style="padding: 10px; border: 1px solid currentColor;">
    <summary>Click to open</summary>
    <p>This text is inside the details element</p>
</details>

### Exclusive accordions

- If multiple `<details> `elements have the same `name` attribute, only one can be open at a time
    - supported in Chrome and Safari as of December 2023

## `＜fieldset＞` and `＜legend＞`

- Group a set of form controls together with an optional title

```html
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
```

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

## `＜link＞`

### Preload files

<div class="rich-link-card-container"><a class="rich-link-card" href="https://www.lkhrs.com/blog/2024/preloading/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.lkhrs.com/swishv2.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Preloading files to reduce download chains in the browser</h1>
		<p class="rich-link-card-description">
		I made a little update to the site today to fix how browsers were loading files needed by other files. The custom font I use and the random quote in the header loads in slightly faster now because the browser is downloading all the files it needs at the same time. My head element looked like this: <head> <link rel="stylesheet" href="css/main.css" /> <link rel="stylesheet" href="css/font.css" /> <script src="js/quotes.js" defer></script> </head> font.
		</p>
		<p class="rich-link-href">
		https://www.lkhrs.com/blog/2024/preloading/
		</p>
	</div>
</a></div>

- assume `styles.css` loads `font1.woff2` and `font2.woff2`, and `script.js` fetches `data.json`
- without preloading, the download chain looks like this, with items on the same level loading in parallel:
    - `styles.css`
        - `font1.woff2`
        - `font2.woff2`
    - `script.js`
        - `data.json`
- with preloading, we can load all the files in parallel
    - note that only the second-tier files use `rel="preload"`

```html
<head>
    <link rel="stylesheet" href="styles.css" />
    <script src="script.js"></script>

    <!-- preloading -->
    <link rel="preload" as="font" crossorigin href="font1.woff2" type="font/woff2" />
    <link rel="preload" as="font" crossorigin href="font2.woff2" type="font/woff2" />
    <link rel="preload" as="fetch" href="data.json" type="application/json" />
</head>
```

- valid embed types
    - `font` requires the `crossorigin` attribute - see [CORS-enabled fetches](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel/preload#cors-enabled_fetches) on MDN
    - using `crossorigin` with `as="fetch"` may cause the preloaded file to be discarded in Chromium and Safari
        - when using `fetch()` in JavaScript to fetch the preloaded file, set `credentials: 'include'` and `mode: 'no-cors'`, and do not include any custom headers

| Value    | Applies To                                                                                                       |
| -------- | ---------------------------------------------------------------------------------------------------------------- |
| audio    | `<audio>` elements                                                                                               |
| document | `<iframe>` and `<frame>` elements                                                                                |
| embed    | `<embed>` elements                                                                                               |
| fetch    | fetch, XHR                                                                                                       |
| font     | CSS @font-face                                                                                                   |
| image    | `<img>` and `<picture>` elements with srcset or imageset attributes, SVG `<image>` elements, CSS `*-image` rules |
| object   | `<object>` elements                                                                                              |
| script   | `<script>` elements, Worker `importScripts`                                                                      |
| style    | `<link rel=stylesheet>` elements, CSS `@import`                                                                  |
| track    | `<track>` elements                                                                                               |
| video    | `<video>` elements                                                                                               |
| worker   | Worker, SharedWorker                                                                                             |

### Preload JavaScript modules

- downloads the module, but also compiles it so it's ready to execute
- no `as` attribute required

```html
<head>
    <link rel="modulepreload" href="module.js" />
</head>
```

## `＜menu＞`

- behaves identically to `<ul>`, but can be used as a semantic alternative

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

### See also

- [[Development/Clipped/Everything I Know About The Script Tag\|Everything I Know About The Script Tag]]

## `＜section＞`

- on content-based pages, should always have a heading (may not apply to web applications)

## `＜table＞`

- `border-spacing: [horizontal] [vertical]` adjusts the gap between cells
- `border-collapse: collapse` removes the gap between cells (overrides `border-spacing`), and prevents borders from doubling up
    - borders on `<tr>` have no effect unless `border-collapse: collapse` is set
- `border-radius` must be applied to `td`, not `<th>`/`<tr>`
    - if you want backgrounds to respect the border radius, you may need to set them on `<td>`

```html
<table>
  <caption style="caption-side: bottom">
    Front-end web developer course 2021
  </caption>
  <thead>
    <tr>
      <th scope="col">Person</th>
      <th scope="col">Most interest in</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Chris</th>
      <td>HTML tables</td>
      <td>22</td>
    </tr>
    <!-- ... -->
  </tbody>
  <tfoot>
    <tr>
      <th scope="row" colspan="2">Average age</th>
      <td>33</td>
    </tr>
  </tfoot>
</table>
```

<table>
  <caption style="caption-side: bottom">
    Front-end web developer course 2021
  </caption>
  <thead>
    <tr>
      <th scope="col">Person</th>
      <th scope="col">Most interest in</th>
      <th scope="col">Age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">Chris</th>
      <td>HTML tables</td>
      <td>22</td>
    </tr>
    <tr>
      <th scope="row">Dennis</th>
      <td>Web accessibility</td>
      <td>45</td>
    </tr>
    <tr>
      <th scope="row">Sarah</th>
      <td>JavaScript frameworks</td>
      <td>29</td>
    </tr>
    <tr>
      <th scope="row">Karen</th>
      <td>Web performance</td>
      <td>36</td>
    </tr>
  </tbody>
  <tfoot>
    <tr>
      <th scope="row" colspan="2">Average age</th>
      <td>33</td>
    </tr>
  </tfoot>
</table>

### `＜caption＞`

- must be the first child of the table
- the `caption-side` CSS property can be used to set the position (`top` or `bottom`)

## `＜video＞`

- `preload` attribute lets you control how much data is preloaded
    - `none`
    - `metadata`: only size, length, etc
    - `auto` (or empty string): can preload the whole video (but not guaranteed)

# Attributes

## inert

- lets you mark an element and its descendants as non-interactive, but still visible
- inert elements:
    - don't receive input
    - can't be focused
    - aren't matched when using the Find feature
    - are hidden from the accessibility tree
- use it on sections of content - if you want to disable an individual control use `disabled` instead
    - example: disabling elements outside of a dialog (though the [[#`<dialog>` (modals/popups)|<dialog>]] element does this by default when opened with `showModal()`)
- don't use it on elements with children that are important to understanding the page, since it will remove them from the accessibility tree

## popover

- lets you show any element as a non-modal overlay on the top layer
    - for modal overlays (which block interaction with the rest of the page), use [[#`<dialog>` (modals/popups)|<dialog>]]
- popovers come in two types:
    - `auto`: can be dismissed by clicking outside, only one can be shown at a time unless they're nested
    - `manual`: must be explicitly closed

### Toggling

- two ways to show a popover:
    - without JavaScript: create a button with `popovertarget="id"`
        - `popovertargetaction` can be set to `show`, `hide`, or `toggle` - default `toggle`
    - with JavaScript: call `showPopover()`, `hidePopover()`, or `togglePopover()` on the popover element
- when toggled, fires a `toggle` event with `oldState === 'closed'` and `newState === 'open'` or vice versa
    - `beforeToggle` is also fired with the same properties just before the popover is shown/hidden

```html
<div id="popoverExample" popover="auto">This is a popover!</div>

<button popovertarget="popoverExample">Show Popover</button>
```

### Styling

- the backdrop can be styled with `::backdrop`
- `:popover-open` targets open popovers
- to animate show/hide:
    - 
<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/notes/notes/css/#with-transition-behavior" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



## With [[Development/Notes/HTML#transition-behavior\|#transition-behavior]]

```css
dialog, [popover] {
    transition-property: opacity, display;
    transition-duration: 0.25s;

    /* when setting `display: none`, keeps the element visible until the end of the transition */
    transition-behavior: allow-discrete;

    @starting-style {
        /* triggers fade in when showing */
        opacity: 0;
    }
}

dialog:not([open]) {
        /* triggers fade out when hiding */
        opacity: 0;
    }
}

[popover]:not(:popover-open) {
        /* triggers fade out when hiding */
        opacity: 0;
    }
}
```


</div></div>


# Events

## `event.target` vs `event.currentTarget`

- `event.currentTarget` is always the element that the ==event listener is attached to==
- `event.target` is the element that ==received the event==
    - in the example below, if you click on the child, `target` is the child and `currentTarget` is the parent

```html
<div id="parent" onClick="handler()">
    <div id="child"></div>
</div>
```

## `input` vs. `change`

- `input` events fire every time the value of a `<input>`, `<select>`, or `<textarea>` element changes
- `change` events only fire when the change is committed by the user (ex. by hitting Enter), or the element loses focus
    - radio buttons do not fire `change` when they are deselected
- ==use `change` for checkboxes & radio buttons==

## `DOMContentLoaded` vs. `load`

The `load` event is fired when the whole page has loaded, including all dependent resources such as stylesheets and images. This is in contrast to `DOMContentLoaded`, which is fired as soon as the page DOM has been loaded, without waiting for resources to finish loading.

The `DOMContentLoaded` event fires when the HTML document has been completely parsed, and all deferred scripts (`<script defer src="…">` and `<script type="module">`) have downloaded and executed. It doesn't wait for other things like images, subframes, and async scripts to finish loading.

`DOMContentLoaded` does not wait for stylesheets to load, however deferred scripts *do* wait for stylesheets, and `DOMContentLoaded` queues behind deferred scripts. Also, scripts which aren't deferred or async (e.g. `<script>`) will wait for already-parsed stylesheets to load.

# Other

## Find focused element

Use `document.activeElement` to find the currently focused element. You can add this as a live expression in the Chrome devtools to get a live updating view of the focused element.

## Quick test image URLs

- width / height, leave out height for a square image

```
https://picsum.photos/200/300
```

- when using the same link multiple times on a page you may get the same image, to work around this you can add a seed

```
https://picsum.photos/seed/seedGoesHere/200/300
```

## Load CSS asynchronously

```html
<link rel="preload" href="styles.css" as="style" onload="this.onload=null;this.rel='stylesheet'">
<noscript><link rel="stylesheet" href="styles.css"></noscript>
```
