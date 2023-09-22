---
{"dg-publish":true,"dg-path":"Cheat sheets/CSS cheat sheet.md","permalink":"/cheat-sheets/css-cheat-sheet/","contentClasses":"css-cheat-sheet"}
---


# Selectors

## Attribute selectors

| Operator | Meaning                                                                           |
| -------- | --------------------------------------------------------------------------------- |
| ~=       | a whitespace-separated list of words, one of which is exactly *value*             |
| \|=      | can be exactly *value* or can begin with *value* immediately followed by a hyphen |
| ^=       | prefixed by *value*                                                               |
| $=       | suffixed by *value*                                                               |
| \*=      | contains *value*                                                                  |
| [... i]  | case insensitive                                                                  |

## :focus-visible

- Like `:focus`, but only applies if the browser decides that focus should be shown visually (typically when using keyboard navigation)
    - Text fields apply `:focus-visible` even when clicked into (in Chrome at least)

## :has

- Matches elements where **any** of the given selectors match relative to the current element
    - Invalid selectors are ignored
- Lets you target elements based on their children or subsequent elements
- Takes the specificity of its most specific argument

> [!warning]
> `:has` **cannot** be nested inside another `:has`!

```css
div:has(+ span) {
    /* matches divs immediately followed by a span */
}

div:has(> span) {
    /* matches divs containing a span */
}

div:has(+ span, > span) {
    /* matches both of the above cases */
}
```

## :is

- Matches any element that can be matched by **any** of the given selectors
    - Invalid selectors are ignored
- Takes the specificity of its most specific argument

```css
:is(ol, ul) :is(ol, ul, :banana /* invalid selector is ignored */) {
    /* matches any nested list, ordered or unordered */
}
```

## :where

- Same as [[Development/Cheat sheets/CSS cheat sheet#:is\|#:is]] but with specificity 0

## :nth-child selectors

- Lets you limit `:nth-child` (and `:nth-last-child`) to only "look at" elements that match the given selector
- Supported by major browsers as of May 2023

```html
<article>
  <h2>Example</h2>
  <img class="hero" src="https://picsum.photos/seed/apple/200/100" />
  <img class="inline" src="https://picsum.photos/seed/orange/200/100" />
  <p>
    Culpa irure anim occaecat ut voluptate duis aliquip consequat esse id id ad
    aute labore. Dolore ipsum ipsum cillum veniam. Nisi ex eiusmod sunt et
    veniam.
  </p>
  <img class="inline" src="https://picsum.photos/seed/banana/200/100" />
  <p>
    Minim magna ullamco nostrud laboris quis reprehenderit minim ex et. Ipsum
    velit Lorem sint ex in aliqua tempor non sunt enim consequat incididunt
    adipisicing do.
  </p>
</article>
```

```css
article {
    max-width: 320px;
}

article > img.hero {
  width: 100%;
  -webkit-margin-after: 1rem;
  margin-block-end: 1rem;
}

article > img.inline {
  float: left;
}
```

- Float inline images in alternating directions

```css
/* this won't work, because both img.inline have odd indexes! */
article > img.inline:nth-child(even) {
	float: right;
}

/* but this will */
article > img:nth-child(even of .inline) {
  float: right;
}
```

- Usage with `~` selector is a bit tricky
    - Select the 2nd `.important` item **after** `.anchor` (should be Item 8)

```html
<ul>
  <li class="important">Item #1</li>
  <li>Item #2</li>
  <li class="anchor">Item #3</li>
  <li>Item #4</li>
  <li class="important">Item #5</li>
  <li>Item #6</li>
  <li>Item #7</li>
  <li class="important">Item #8</li>
  <li class="important">Item #9</li>
  <li>Item #10</li>
</ul>
```

```css
/* this will select Item #5, because it's the 2nd child with .important */
li.anchor ~ :nth-child(2 of .important) {
	color: lime;
}

/* But this will work */
:nth-child(2 of li.anchor ~ .important) {
	color: deepskyblue;
}
```

# Properties

## aspect-ratio

- Keeps the element at the specified aspect ratio (`width / height`)

```css
aspect-ratio: 16 / 9;
```

## clip-path

<div class="rich-link-card-container"><a class="rich-link-card" href="https://bennettfeely.com/clippy/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://bennettfeely.com/clippy/homescreen-196x196.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Clippy — CSS clip-path maker</h1>
		<p class="rich-link-card-description">

		</p>
		<p class="rich-link-href">
		https://bennettfeely.com/clippy/
		</p>
	</div>

</a></div>

### inset

- Simple insets from each side, clockwise from top

```css
clip-path: inset(5% 10% 15% 20%);
```

### polygon

- Can have any number of points, values are `<x> <y>`

```css
/* equilateral triangle */
clip-path: polygon(50% 0, 0% 100%, 100% 100%);

/* trapezoid that narrows at the top */
clip-path: polygon(20% 0, 80% 0, 100% 100%, 0% 100%);
```

### circle

- `radius at center`

```css
clip-path: circle(50% at 50% 50%);
```

### ellipse

- `radiusX radiusY at center`

```css
clip-path: ellipse(25% 40% at 50% 50%);
```

## contain

- Lets you isolate an element and its contents from the rest of the document
- Values (multiple can be used, separated with spaces):
    - `size`: the element's size is unaffected by its children
        - `inline-size`: same as `size` but for inline-size only (can't be combined with `size`)
    - `layout`: nothing outside the element affects the element's internals, and vice versa
        - `position: fixed` children won't escape the element, and `float`ing children won't affect the rest of the page
    - `style`: counters and quotes are scoped to the element and its contents
    - `paint`: the element's contents will not be drawn outside its bounds
        - like `overflow: hidden` but also applies to text
    - `strict`: same as `size layout paint style`
    - `content`: same as `layout paint style`
- Use `contain: content` for elements such as articles that are independent from the rest of the page

## content-visibility

- Controls whether the browser renders the element's contents
- `hidden`: the element is never rendered
- `auto`: the element is only rendered if it is "relevant to the user" - in or near the viewport, focused, selected, or in the top layer

## gap

- Sets spacing between tracks in flex or grid views
    - Unlike margins, `gap` doesn't apply to the outer edges
- First value is gap between rows, second is gap between columns
    - If only one value is given, it applies to both
    - There are also separate `row-gap` / `column-gap` properties

```css
gap: 10px 5px;
```

## mask properties

- Requires `-webkit-` prefix on Chromium browsers
- Useful for [changing the color of SVG icons](https://codepen.io/noahblon/post/coloring-svgs-in-css-background-images) - set the `background-color` as the color you want and use the SVG as the `mask-image`

```css
mask-image: url('image.svg');
mask-mode: alpha; /* or luminance */
mask-composite: add; /* or subtract, intersect, exclude - if you have multiple masks, this controls how each one is composited with the masks below it */

/* these accept the same values as their background- equivalents */
mask-size: auto;
mask-position: center;
mask-clip: border-box;
mask-repeat: repeat;
```

## outline-offset

- Adjusts the amount of space between an element's edge and its outline, can be positive or negative

<div class="outline-offset-example">
    <div style="outline-offset: 10px">
    outline:offset: 10px
    </div>
    <div style="outline-offset: -10px">
    outline:offset: -10px
    </div>
</div>

## overscroll-behavior

- `contain`: prevents scroll chaining (when scrolling past the edge of the container starts to scroll outside the container)
- `none`: prevents scroll chaining, and also prevents the "bounce" effect and pull to refresh

## resize

- doesn't work on inline elements or if `overflow` is `visible`

## user-select

- Lets you control how text selection in an element works, or disable it entirely
    - `none`: disable text selection within the bounds of this element
    - `all`: select all the text within this element on click
    - `contain`: limit the text selection to the bounds of this element

> [!important]
> `user-select: none` should be used sparingly, and only for UI text that a user isn't likely to want to copy.

```css
user-select: none;
```

## white-space

- spaces include space characters, tabs, and segment breaks (such as newlines)
- *hang* means that the character may be placed outside the box and does not affect sizing

|                | New lines | Spaces and tabs | Text wrapping | End-of-line spaces | End-of-line other space separators |
|:-------------- |:--------- |:--------------- |:------------- |:------------------ |:---------------------------------- |
| `normal`       | Collapse  | Collapse        | Wrap          | Remove             | Hang                               |
| `nowrap`       | Collapse  | Collapse        | No wrap       | Remove             | Hang                               |
| `pre`          | Preserve  | Preserve        | No wrap       | Preserve           | No wrap                            |
| `pre-wrap`     | Preserve  | Preserve        | Wrap          | Hang               | Hang                               |
| `pre-line`     | Preserve  | Collapse        | Wrap          | Remove             | Hang                               |
| `break-spaces` | Preserve  | Preserve        | Wrap          | Wrap               | Wrap                               |

## Shorthands

### background

- separate position and size (in that order) with a slash: `center/80%` or `50% 50% / 0% 0%`
- If only one `box` value is specified, it sets both `origin` and `clip`
- `color` can only be specified on the last layer

| Property   | Default     |
| ---------- | ----------- |
| image      | none        |
| position   | 0% 0%       |
| size       | auto auto   |
| origin     | padding-box |
| clip       | border-box  |
| attachment | scroll      |
| repeat     | repeat      |
| color      | transparent |

```css
background: repeat scroll 0% 0%/auto padding-box border-box none transparent;
```

### box-shadow

inset? | offset-x | offset-y | blur-radius | spread-radius | color

```css
box-shadow: 3px 3px red, inset -1em 0 0.4em blue;
```

### text-shadow

offset-x | offset-y | blur-radius | color

```css
text-shadow: 1px 1px 2px black, 0 0 1em blue, 0 0 0.2em blue;
```

### animation

| Property        | Default |
| --------------- | ------- |
| duration        | 0s      |
| timing-function | ease    |
| delay           | 0s      |
| iteration-count | 1       |
| direction       | normal  |
| fill-mode       | none    |
| play-state      | running |
| name            | none    |

```css
animation: 3s ease-in 1s infinite reverse both running slidein;
```

# Functions

## minmax

- Used with grids
- Item width will be >= min and <= max
- use `minmax(0, 1fr)` to keep all rows/columns the same size

## color-mix

```css
color-mix(in oklab, red 25%, blue)
```

- There are lots of different color spaces, `oklab` tends to give the most "natural-looking" result

![[Pasted image 20230812150956.png\|Pasted image 20230812150956.png]]

## cross-fade

### Implemented syntax

> [!warning]
> Firefox doesn't support this syntax!
> Safari 16 doesn't correctly handle `linear-gradient` inside `-webkit-cross-fade`.

- Only supports two images, opacity applies to the first image

```css
-webkit-cross-fade(url(white.png), url(black.png), 0%) /* fully black */
-webkit-cross-fade(url(white.png), url(black.png), 100%) /* fully white */
```

### Specification syntax

> [!warning]
> As of May 2023 no browsers support this syntax yet!

- Supports any number of images, with later images on top
- If any opacity percentages are left out, it will evenly distribute them to reach 100%

```css
cross-fade(url(white.png), url(black.png)) /* 50% white 50% black */
cross-fade(url(white.png), url(black.png) 75%) /* 25% white 75% black */
cross-fade(url(white.png), url(black.png) 100%) /* 100% black */
```

# At-rules

## @import

- Avoid using `@import`, as it makes the browser download CSS sequentially and slows down rendering. Instead, link the stylesheets separately in your HTML, or use a bundler to combine them into one stylesheet.

## @property

- Allows you to define CSS custom properties with control over data type, inheritance, and initial value
    - can be used to create animatable/transition-able custom properties

```css
@property --property-name {
  syntax: "<color>";
  inherits: false;
  initial-value: #c0ffee;
}
```

- Allowed `syntax` values:
    - `<length>`
    - `<number>`
    - `<percentage>`
    - `<length-percentage>` (same as `<length> | <percentage>`)
    - `<color>`
    - `<image>`
    - `<url>`
    - `<integer>`
    - `<angle>`
    - `<time>`
    - `<resolution>`
    - `<transform-function>`
    - `<transform-list>`
    - `<custom-ident>` (see examples)

- Examples:

```css
syntax: "<color>";
syntax: "<length> | <percentage>";
syntax: "small | medium | large"; /* custom ident example */
syntax: "*"; /* any value */
```

## @layer (Cascade Layers)

<div class="rich-link-card-container"><a class="rich-link-card" href="https://www.bram.us/2021/09/15/the-future-of-css-cascade-layers-css-at-layer/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.bram.us/wordpress/wp-content/uploads/2021/09/css-cascade-cascade-layers-champ-bramus.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">The Future of CSS: Cascade Layers (CSS @layer)</h1>
		<p class="rich-link-card-description">
		When authoring CSS we have to carefully think about how we write and structure our code. Cascade Layers (CSS @layer) aim to ease this task.
		</p>
		<p class="rich-link-href">
		https://www.bram.us/2021/09/15/the-future-of-css-cascade-layers-css-at-layer/
		</p>
	</div>
</a></div>

- Layers that are declared later take higher priority
- You can declare layers at the same time as adding rules, but it's clearer to declare the layer names first and then add rules
- You can also specify a layer name when using `@import` (though you should [[Development/Cheat sheets/CSS cheat sheet#@import\|avoid using @import]])

```css
@layer reset, base, theme, utilities;

@import url(reset.css) layer(reset);

@layer base {
    button.filled {
        background-color: black;
        color: white;
    }
}

@layer theme {
    button {
        background-color: blue;
    }
    button.unthemed {
        background-color: revert-layer;
    }
}
```

- This button will have a **blue** background, because even though the rule in the *base* layer is more specific, the *theme* layer wins because it has a higher priority

```html
<button class="filled">Click me!</button>
```

> [!important]
> Unlayered rules take priority over layered rules!

- Use the `revert-layer` keyword to "roll back" a value to the previous layer
    - Behaves like `revert` if used outside a layer
- This button will have a **black** background

```html
<button class="filled unthemed">Click me!</button>
```

- When using `!important`, the layer order is **reversed**: a `!important` rule in *base* will override a `!important` rule in *theme*
    - this means that `!important` rules in layers override unlayered `!important` rules
- Layers can have sub-layers, which can be referred to using dot notation

```css
@layer framework {
    @layer base {
        ...
    }
    @layer theme {
        ...
    }
}

@layer framework.theme {
    ...
}
```

## @container (container queries)

- Style elements based on the size of a container's **content-box** (padding isn't included, even if the element has `box-sizing: border-box`)
- Supported in all major browsers as of February 2023
- Containers are marked with the `container-type` property
    - two values: `inline-size` allows you to query the inline size only, `size` allows you to query both directions
        - use `size` sparingly for performance reasons
- Supported query conditions: `width`, `height`, `block-size`, `inline-size`, `aspect-ratio`, `orientation`
    - the first 4 require containment in that direction (obviously), `aspect-ratio` and `orientation` require `container-type: size`
- By default, rules in container queries apply to the nearest ancestor container
- Rules are scoped to the container - in the example below, the `.grid` rule will only apply to `.grid` elements inside `<section>`
- Container queries have their own length units that correspond to viewport units - `cqw` for `vw`, `cqmin` for `vmin`, etc
    - If not in a container, these units equal their corresponding viewport unit

Create a grid that collapses when its container is below a certain size:

```html
<section>
    <div class="grid"></div>
</section>
```

```css
section {
    container-type: inline-size;
}

.grid {
	grid-template-columns: repeat(auto-fit, minmax(0%, 1fr));
}

@container (max-width: 500px) {
	.grid {
		grid-template-columns: 1fr;
	}
}
```

- You can name containers using `container-name`, and reference them by writing their name after `@container`
    - shorthand: `container: name / type`

```html
<article>
    <h2>Baked Ziti</h2>

    <section>
        <h3>Ingredients</h3>
        ...
    </section>

    <section>
        <h3>Directions</h3>
        ...
    </section>
</article>
```

```css
article {
    container-name: recipe;
    container-type: inline-size;
}

article section {
    /* shorthand */
    container: recipe-section / inline-size;
}

/* header size is based on the <article> width, so the section headers will
be the same size regardless of the section width */
@container recipe (min-width: 500px) {
    h3 {
        font-size: 1.33em;
    }
}
```

# Media queries

## Dark mode

```css
@media (prefers-color-scheme: dark) (
    /* dark mode styles */
)
```

## Reduce motion

```css
@media (prefers-reduced-motion: reduce) {
    /* disable transitions or replace them with a simple fade */
}
```

# Flexbox

By default flex items have `min-width: auto`, meaning they can't be smaller than their content. If flex items are overflowing their container, set `min-width: 0` or `overflow:hidden` on them.

# Grid

- Use `order` to rearrange grid items
- Use [[Development/Cheat sheets/CSS cheat sheet#gap\|#gap]] to create gutters between grid tracks

## Grid container properties

### grid-template-columns, grid-template-rows

- Defines the count and size of explicit grid tracks (rows or columns)

```css
grid-template-columns: 50% 50%;
/* these are the same */
grid-template-rows: 20% 20% 20% 20% 20%;
grid-template-rows: repeat(5, 20%);
```

<div class="grid-example" style="grid-template-columns: 50% 50%; grid-template-rows: repeat(5, 20%);">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

- The `fr` unit represents one "part" of the available space
- If some columns are defined with lengths or percentages, `fr` divides the remaining space

```css
grid-template-columns: 1fr 4fr; /* same as 20% 80% */
grid-template-rows: 50px repeat(3, 1fr) 50px;
```

#### auto-fill

- Use the `auto-fill` keyword to create as many tracks as will fit in the container

```css
grid-template-columns: repeat(auto-fill, 200px);
```

<div class="grid-example" style="grid-template-columns: repeat(auto-fill, 200px); background-color: var(--blockquote-background-color); ">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

#### auto-fit

- Use `auto-fit` with [[Development/Cheat sheets/CSS cheat sheet#minmax\|#minmax]] to make the tracks expand to fit any leftover space

```css
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

<div class="grid-example" style="grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); background-color: var(--blockquote-background-color);">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

### grid-template

- Shorthand for the above: `rows / columns`

### grid-auto-flow

- Controls the direction (`row` or `column`) that implicit grid tracks are created in (default: `row`)
- Add `dense` to "fill in" holes earlier in the grid (this may cause items to display out of order)

### grid-auto-rows, grid-auto-columns

- By default implicit rows/columns are sized to fit their content, this property lets you give them an explicit size

```css
grid-auto-rows: 100px;
grid-auto-columns: minmax(100px, auto);
```

### align-items, justify-content

- Works like in flexbox, except with `start` and `end` rather than `flex-start` and `flex-end`
{ #0c043d}

    - Aligns each item within its grid area (could span many cells)

## Grid item properties

### grid-row-start, grid-column-start, grid-row-end, grid-column-end

- Set which grid **lines** (not tracks!) an element's stating or ending edges touch (1-indexed)
- An element with `start: 1` and `end: 4` will span three cells

<div class="grid-example number-lines" style="grid-template-columns: repeat(5, 1fr);">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <span class="active abs-fill" style="grid-column-start: 1; grid-column-end: 4;"></span>
</div>

- Order doesn't matter when using integers: `start: 4, end: 1` is the same as `start: 1, end: 4`
- Negative values start counting from the end: `start: -2` will align the starting edge of the element to the next-to-last grid line
    - You can mix positive and negative: `start: 1, end: -1` will span the whole track
- Use the `span` keyword to declare how many cells an element takes up

```css
/* this element will be 2 cells wide */
grid-column-start: 2;
grid-column-end: span 2;
```

- If the specified lines are outside the bounds of the [[Development/Cheat sheets/CSS cheat sheet#grid-template\|#grid-template]], the browser will generate implicit grid tracks for the item
    - You can use this and [[Development/Cheat sheets/CSS cheat sheet#grid-auto-flow\|#grid-auto-flow]] to accomplish some layouts without defining a `grid-template` at all

### grid-row, grid-column

- Shorthand for the above: `start / end`

### grid-area

- Shorthand for `grid-row-start / grid-column-start / grid-row-end / grid-column-end`

### align-self


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/cheat-sheets/css-cheat-sheet/#0c043d" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



- Works like in flexbox, except with `start` and `end` rather than `flex-start` and `flex-end` 

</div></div>


### position: absolute

- Grid items with `position: absolute` will take their grid area as their containing block if they have one, or the entire grid if they don't
    - Make sure the grid container has `position: relative`

<div class="grid-example" style="position: relative; grid-template-columns: repeat(3, 1fr);">
    <div></div>
    <div class="active"></div>
    <div></div>
    <div class="abs-fill" style="background-color: deepskyblue; grid-column: 2 / 3; left: 50px; top: 20px;"></div>
</div>

### display: contents

- Use `display: contents` to group grid children together, while still laying them out as part of the grid

<div class="grid-example" style="grid-template: repeat(2, 1fr) / repeat(5, 1fr)">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div aria-label="Active Items" style="display: contents">
        <div class="active"></div>
        <div class="active"></div>
        <div class="active"></div>
        <div class="active"></div>
        <div class="active"></div>
    </div>
    <div></div>
</div>

## See also

[[Development/Clipped/Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers\|Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers]]

# Other

## Font weights

| Value | Common weight name        |
| ----- | ------------------------- |
| 100   | Thin (Hairline)           |
| 200   | Extra Light (Ultra Light) |
| 300   | Light                     |
| 400   | Normal (Regular)          |
| 500   | Medium                    |
| 600   | Semi Bold (Demi Bold)     |
| 700   | Bold                      |
| 800   | Extra Bold (Ultra Bold)   |
| 900   | Black (Heavy)             |
| 950   | Extra Black (Ultra Black) |

## `unset` vs. `revert`

- `unset` uses the *property's* inherited value if it inherits, or its initial value if not
- `revert` uses the *element's* default style (as set by the browser), unless the user has applied a custom style, in which case it uses that value
    - In practice, most methods of applying custom styles (such as [Stylus](https://add0n.com/stylus.html)) apply them to the `author` style origin, so `revert` will roll back to the browser's style anyway

For example, the default value of the `display` property is `inline`, but browsers set `<div>` elements to `display: block`.

Setting `display: unset` on a `<div>` will apply `display: inline`, which probably isn't what you want. Setting `display: revert` instead will apply `display: block`.

## `opacity: 0` vs. `visibility: hidden`

- Elements with `opacity: 0` are still:
    - clickable (will fire click events)
    - focusable
    - visible to screen readers
- Elements with `visibility: hidden` don't do any of the above.

## Negative transition/animation-delays

A negative `transition-delay` or `animation-delay` will start the transition/animation partway through. For example, `transition-delay: -50ms` will start the transition at the 50ms mark.

## Transition from height 0 to auto

- Place the element in a container with this styling

```css
display: grid;
grid-template-rows: 0fr;
transition: grid-template-rows;
```

- To transition to auto height, change `grid-template-rows` to `1fr`

<iframe width="560" height="315" src="https://www.youtube.com/embed/B_n4YONte5A" title="YouTube video player" frameborder="0" allow="encrypted-media; picture-in-picture; web-share" allowfullscreen></iframe>

## Nested backdrop filters

<div class="rich-link-card-container"><a class="rich-link-card" href="https://stackoverflow.com/a/76207141" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://cdn.sstatic.net/Sites/stackoverflow/Img/apple-touch-icon@2.png?v=73d79a89bded')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">backdrop-filter not working for nested elements in Chrome</h1>
		<p class="rich-link-card-description">
		I have a div.outer and inside a div.inner, both with position: absolute; and backdrop-filter: blur(8px);.

https://jsbin.com/nihakiqocu/1/edit?html,css,output

Safari (left) gives the desired resul...

		</p>

		<p class="rich-link-href">

		https://stackoverflow.com/a/76207141

		</p>

	</div>

</a></div>

Setting a `backdrop-filter` on an element turns it into a *backdrop root*. Child elements can't "see through" a backdrop root, so `backdrop-filter` on children won't work as expected.

To fix this, add the `backdrop-filter` to a pseudo-element on the parent:

```css
.blurred-bg::before {
    content: '';
    position: absolute;
    width: 100%;
    height: 100%;
    backdrop-filter: blur(30px);
    z-index: -1;
}
```

## Custom property quirks

- The browser first determines the cascaded value for an element, then "throws away" all other possible cascade values, before checking if the value is valid

```css
html { color: red; }

/* gets thrown away because the value on `.card p` is more specific */
p { color: blue; }

.card { --color: #notacolor; }

/* Gets selected as the final *cascaded* value. But since it's invalid, and the value on `p` was already thrown away, falls back to the *inherited* value from `html` */
.card p { color: var(--color); }
```

- Custom properties are computed before inheritance happens

```css
:root {
    --spacing: 0.5rem;
    --spacing-small: calc(var(--spacing) / 2); /* 0.25rem */
}

.element {
    --spacing: 1rem;
    /* --spacing-small is still 0.25rem because the value is
    "baked in" at the root level */
}
```

<div class="rich-link-card-container"><a class="rich-link-card" href="https://moderncss.dev/how-custom-property-values-are-computed/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://moderncss.dev/img/social/how-custom-property-values-are-computed.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">How Custom Property Values are Computed | Modern CSS Solutions</h1>
		<p class="rich-link-card-description">
		Review behaviors to be aware of regarding how the browser computes final custom property values. A misunderstanding of this process may lead to an unexpected or missing value and difficulty troubleshooting and resolving the issue.
		</p>
		<p class="rich-link-href">
		https://moderncss.dev/how-custom-property-values-are-computed/
		</p>
	</div>
</a></div>

# See also

- [[Development/Cheat sheets/Sass cheat sheet\|Sass cheat sheet]]
- [[Development/Clipped/Useful nth-child Recipes\|Useful nth-child Recipes]]
