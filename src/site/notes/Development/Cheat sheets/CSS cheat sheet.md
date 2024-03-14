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

## :user-valid and :user-invalid

- like `:valid` and `:invalid`, but only apply once the user has interacted with the form element
    - useful for marking invalid fields without marking the entire page as invalid on load

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
    /* matches either of the above cases */
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

## :nth-child with selectors

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

- Float inline images in alternating directions

```css
article > img.inline {
  float: left;
}

/* this won't work, because both img.inline have odd indexes! */
article > img.inline:nth-child(even) {
	float: right;
}

/* but this will */
article > img:nth-child(even of .inline) {
  float: right;
}
```

- The `nth-child` selector can't "see" selectors outside itself
- Select the 2nd `.important` item **after** `.anchor` (should be Item 8)

```html
<ul>
  <li class="important">Item 1</li>
  <li>Item 2</li>
  <li class="anchor">Item 3</li>
  <li>Item 4</li>
  <li class="important">Item 5</li>
  <li>Item 6</li>
  <li>Item 7</li>
  <li class="important">Item 8</li>
  <li class="important">Item 9</li>
  <li>Item 10</li>
</ul>
```

```css
/* this will select item 5 - it's after the anchor, and *separately* it's the second child with .important */
li.anchor ~ :nth-child(2 of .important) {
	color: lime;
}

/* This will select item 8, because it's the second child *after the anchor with .important* */
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

## background

- if you have multiple backgrounds, earlier ones are drawn on top

## clip-path

<div class="rich-link-card-container"><a class="rich-link-card" href="https://bennettfeely.com/clippy/" target="_blank">
		<div class="rich-link-image-container">
			<div class="rich-link-image" style="background-image: url('https://bennettfeely.com/_img/screenshots/clippy.png')">
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

## color-scheme

- Automatically adapt to the user's system color scheme
    - `light` or `dark` will force the element to the specified color scheme
    - `light dark` means the element should support light and dark modes, and prefer light mode
    - `dark light` is the same, but will prefer dark mode
    - `only light` or `only dark` prevents the browser from overriding the color scheme
- Use [[Development/Cheat sheets/CSS cheat sheet#prefers-color-scheme\|prefers-color-scheme]] as usual to style elements based on the color scheme
    - consider using [[Development/Cheat sheets/CSS cheat sheet#System colors\|system color]] keywords to make the page match the system
- Add a `color-scheme` meta tag to render the correct page background before the CSS loads

```html
<meta name="color-scheme" content="dark-light">
```

Demo: https://color-scheme-demo.glitch.me/

## contain

- Isolate an element and its contents from the rest of the document
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

## forced-color-adjust

- use `none` to override an element's colors when in [[Development/Cheat sheets/CSS cheat sheet#forced-colors\|#forced-colors]] mode
    - only use this if the colors the browser applies aren't readable

```css
.button {
    forced-color-adjust: none;
}
```

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

## mix-blend-mode

- controls how an element blends with its background
- use `mix-blend-mode: difference` to reverse text color of a progress bar

<div class="rich-link-card-container"><a class="rich-link-card" href="https://css-tricks.com/reverse-text-color-mix-blend-mode/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://css-tricks.com/wp-json/social-image-generator/v1/image/209684')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Reverse Text Color Based on Background Color Automatically in CSS | CSS-Tricks</h1>
		<p class="rich-link-card-description">
		Over the weekend I noticed an interesting design for a progress meter in a videogame. The % complete was listed in text in the middle of the bar and didn't
		</p>
		<p class="rich-link-href">
		https://css-tricks.com/reverse-text-color-mix-blend-mode/
		</p>
	</div>
</a></div>

## overscroll-behavior

- `contain`: prevents scroll chaining (when scrolling past the edge of the container starts to scroll outside the container)
- `none`: prevents scroll chaining, and also prevents the "bounce" effect and pull to refresh

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

## width

- `min-content`: wrap text content as much as possible
- `fit-content`: fit to the content, wrapping if necessary
- `max-content`: don't wrap text content at all

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

# Flexbox

## Make flex children the same size

Use `flex: 1 1 100%` to make every flex child the same size, regardless of content.

## Prevent flex items from overflowing container

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

<div class="grid-example" style="grid-template-columns: repeat(auto-fill, 200px); background-color: var(--background-secondary); ">
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

<div class="grid-example" style="grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); background-color: var(--background-secondary);">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

### grid-template

- Shorthand for the above: `grid-template-rows / grid-template-columns`

```css
grid-template: repeat(auto-fill, 200px) / repeat(2, 1fr);
```

### grid-auto-flow

- Controls the direction (`row` or `column`) that implicit grid tracks are created in (default: `row`)
- Add `dense` to "fill in" holes earlier in the grid - this may cause items to display out of order

### grid-auto-rows, grid-auto-columns

- By default, implicit rows/columns are sized to fit their content, this property lets you give them an explicit size

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
    <div style="display: contents">
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

# Functions

## min, max, clamp

- you can perform calculations inside these functions without needing `calc()`

```css
width: min(100vw - 3rem, 80ch)
```

## clamp

```css
clamp(min, ideal, max)
```

- the middle value will be used as long as it's between min and max
- `min` and `max` should use a different unit than `ideal`
- in this example, the font size will adjust with the viewport width, but will never be smaller than `1rem` or larger than `3rem`

```css
font-size: clamp(1rem, 4vw, 3rem)
```

## minmax

- Used with grids
- Item width will be >= min and <= max
- use `minmax(0, 1fr)` to keep all rows/columns the same size

## color-mix

```css
color-mix(in oklab, red 25%, blue)
```

- There are lots of different color spaces, `oklab` tends to give the most "natural-looking" result

![Pasted image 20230812150956.png](/img/user/Development/%E2%80%A2%20Attachments/Pasted%20image%2020230812150956.png)

## Relative color syntax

- lets you create a color from another color without needing [[Development/Cheat sheets/CSS cheat sheet#color-mix\|#color-mix]], and edit individual color components
    - supported in Chrome and Safari as of December 2023

```css
/* the input color can be in any format */*
color: rgb(from deepskyblue r g b);

/* can mix and match the individual components and numbers */
color: rgb(from deepskyblue g g 0);

/* or do math on them */
/* r g b channels are out of 255 */
color: rgb(from deepskyblue r calc(g * 2) b);

/* h is out of 255, s and l are out of 1 */
color: hsl(from deepskyblue h calc(s + .5) l);
```

### Adjust transparency/alpha

- alpha is separated by a slash

```css
/* reduce opacity by 50% */
color: rgb(from deepskyblue r g b / calc(alpha * 0.5))
/* reduce to 25% opacity */
color: hsl(from deepskyblue h s l / 0.25);
```

### Lighten or darken a color

- OKLAB provides the most reliable results (see [[Development/Cheat sheets/CSS cheat sheet#color-mix\|#color-mix]])

```css
/* lightens the color by 25% */
color: oklab(from deepskyblue calc(l + 25) a b);

/* adjusts to 75% lightness regardless of the original value */
color: oklab(from deepskyblue 75% a b);
```

### Saturate a color

```css
color: hsl(from deeppink h calc(s + 0.5) l);
```

- use chroma instead of saturation to enter high dynamic range

```css
color: oklch(from deeppink l calc(c + 0.5) h);
```

### Create a color palette from a single color

Varied lightness:

```css
:root {
  --base-color: deeppink;

  --color-0: oklch(from var(--base-color) calc(l + 20) c h); /* lightest */
  --color-1: oklch(from var(--base-color) calc(l + 10) c h);
  --color-2: var(--base-color);
  --color-3: oklch(from var(--base-color) calc(l - 10) c h);
  --color-4: oklch(from var(--base-color) calc(l - 20) c h); /* darkest */
}
```

Hue rotation:

```css
:root {
  --base-color: blue;

  --primary:   var(--base-color);
  --secondary: oklch(from var(--base-color) l c calc(h - 45));
  --tertiary:  oklch(from var(--base-color) l c calc(h + 45));
}
```

Combine the two:

```css
:root {
  --base-color: deeppink;

  --color-1: var(--base-color);
  --color-2: oklch(from var(--base-color) calc(l - 10) c calc(h - 10));
  --color-3: oklch(from var(--base-color) calc(l - 20) c calc(h - 20));
  --color-4: oklch(from var(--base-color) calc(l - 30) c calc(h - 30));
  --color-5: oklch(from var(--base-color) calc(l - 40) c calc(h - 40));
}
```

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
> - not supported by Firefox
> - supported with `-webkit-` prefix in Chrome, and unprefixed in Safari

- Supports any number of images, with later images on top
- If any opacity percentages are left out, it will evenly distribute them to reach 100%

```css
cross-fade(url(white.png), url(black.png)) /* 50% white 50% black */
cross-fade(url(white.png), url(black.png) 75%) /* 25% white 75% black */
cross-fade(url(white.png), url(black.png) 100%) /* 100% black */
```

# Keywords

## fit-content, min-content, max-content

- when used with `width`:
    - `min-content` will fit to the width of the longest word
    - `fit-content` will contain the contents but not overflow
    - `max-content` will grow as large as the contents require, including overflow

# At-rules

## @import

==Avoid using `@import`==, as it makes the browser download CSS sequentially and slows down rendering. Instead, link the stylesheets separately in your HTML, or use a bundler to combine them into one stylesheet.

## @media (media queries)

### prefers-color-scheme

```css
@media (prefers-color-scheme: dark) { }

@media (prefers-color-scheme: light) { }
```

### Hover support

```css
@media (hover: hover) { }
```

### prefers-reduced-motion

```css
@media (prefers-reduced-motion: reduce) {
    /* disable transitions or replace with a simple fade */
}
```

### prefers-reduced-transparency

- as of October 2023 supported by Chrome, not Firefox or Safari

```css
@media (prefers-reduced-transparency: reduce) {
    /* remove transparency and backdrop-filters */
}
```

### forced-colors

- detect high contrast mode and other situations where the browser is overriding the page colors
    - should be used for small tweaks only - ==respect the user's choices and don't try to override them==
- in forced colors mode, the browser forcibly applies appropriate [[Development/Cheat sheets/CSS cheat sheet#System colors\|system colors]] to elements, and removes shadows and non-URL background-images (like gradients)
- also see [[Development/Cheat sheets/CSS cheat sheet#forced-color-adjust\|#forced-color-adjust]]

```css
@media (forced-colors: active) { }
```

### Boolean operators

#### and

```css
@media (orientation: portrait) and (min-width: 300px) {
    /* landscape *and* at least 300px wide */
}
```

#### Multiple queries (or)

- use a comma to match one of several queries, just like with selectors

```css
@media (orientation: landscape), (min-width: 300px) {
    /* landscape *or* at least 300px wide */
}
```

#### not

```css
@media not (hover) { }
```

- `not` applies to an entire query, but only to one query in a comma-separated list

```css
@media not screen and (color) { }
/* equivalent to */
@media not (screen and (color)) { }

@media not screen and (color), print and (color) { }
/* equivalent to */
@media (not (screen and (color))), print and (color) { }
```

### Ranges

```css
@media (width >= 300px) { }
```

## @scope

- lets you limit a rule's reach based on a parent selector

```css
@scope (.card) {
    img {
        /* only images inside .card elements get this border */
        border: 2px solid black;
    }
}
```

- `:scope` matches the scope root
- `&` matches *the selector* used for the scope root - the difference is that `&` can be used multiple times

```css
@scope (.card) {
    :scope {
        /* selects the .card element itself */
    }

    & & {
        /* selects a .card inside the matched .card */
    }
}
```

- `@scope` can accept a second argument to limit the reach
    - inheritance still passes through the scope bounds

```css
@scope (.card) to (.card-content) {
    /* p elements inside .card-content won't be affected */
    p {
        font-weight: bold;
    }

    :scope {
        /* but they'll still inherit this text color if it's not overridden */
        color: gray;
    }
}

@scope (.card) to (:scope > .content) {
    /* elements inside .content won't be affected, but only if .content is a direct child of .card */
}

/* the limit can reference elements outside the scope */
@scope (.card) to (.sidebar :scope .content) {]
    /* .content only limits the scope if the card is inside .sidebar */
}
```

- `@scope` doesn't affect specificity
- `:scope` has specificity of `(0, 1, 0)` (same as any pseudo-class)
- if two `@scope` selectors have the same specificity, the one with a closer scope root wins
    - in the example below, the `p` element is white because `.light` is closer than `.dark`

```html
<div class="dark">
    <div class="light">
        <p>
    </div>
</div>
```

```css
@scope (.light) {
    p {
        color: white;
    }
}

@scope (.dark) {
    p {
        color: white;
    }
}
```

## @supports

- lets you provide a property and value to test for browser support

```css
@supports (color: rgb(from white r g b)) {
    /* this browser supports relative color syntax */
}
```

## @property

- lets you define CSS custom properties with control over data type, inheritance, and initial value
    - can be used to create animatable/transitionable custom properties

```css
@property --property-name {
  syntax: "<color>";
  inherits: false;
  initial-value: #c0ffee;
}
```

- Aallowed `syntax` values:
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

## @container (container queries)

- Supported in all major browsers as of February 2023
- Style elements based on the size of a container's **content-box** (padding isn't included, even if the element has `box-sizing: border-box`)
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

# CSS Nesting

- Supported in all major browsers as of August 2023
    - starting nested rules with a type (element) selector without using `&` is currently inconsistent

```css
.foo {
    .bar {
        /* equivalent to .foo .bar */
    }

    & .bar {
        /* same as above */
    }

    .bar & {
        /* .bar .foo */
    }

    &.bar {
        /* .foo.bar (note the lack of space) */
    }

    + .bar {
        /* .foo + .bar */
    }

    div {
        /* this will be .foo div, but browser support is inconsistent - use "& div" instead */
    }

    /* ⛔️ you can't concatenate strings */
    &__bar {
        /* this won't equal .foo__bar! */
    }

    /* but you can do this, as long as the type selector comes first (but note the caveat about browser support) */
    div& {
        /* div.foo */
    }

    /* you can use & multiple times */
    &:nth-child(1 of &) {
        /* matches the first .foo within its parent */
    }
}
```

## Nested @rules

- you can nest media queries and other @rules, and leave out the selector if it's the same

```css
.foo {
    color: black;

    @media (orientation: landscape) {
        /* by default matches the parent selector (.foo) */
        color: white;
        
        .bar {
            /* matches .foo .bar, but only in landscape */
        }

        @media (min-width > 1024px) {
            /* you can even nest multiple layers of @rules */
            /* equivalent to @media (orientation: landscape) and (min-width > 1024px) */
        }
    }
}
```

- [[Development/Clipped/The Future of CSS Cascade Layers (CSS @layer)\|cascade layers]] can be nested, which creates a new sub-layer joined with a dot

```css
@layer base {
    @layer support {
        /* creates a layer called base.support */
    }
}
```

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

## System colors

- use colors provided by the operating system
- browser support is inconsistent, so use [[Development/Cheat sheets/CSS cheat sheet#@supports\|#@supports]] to test and set fallback values appropriately

| Keyword           | Description                                                                          |
| ----------------- | ------------------------------------------------------------------------------------ |
| `AccentColor`     | Background of accented user interface controls                                       |
| `AccentColorText` | Text of accented user interface controls                                             |
| `ActiveText`      | Text of active links                                                                 |
| `ButtonBorder`    | Base border color of controls                                                        |
| `ButtonFace`      | Background color of controls                                                         |
| `ButtonText`      | Text color of controls                                                               |
| `Canvas`          | Background of application content or documents                                       |
| `CanvasText`      | Text color in application content or documents                                       |
| `Field`           | Background of input fields                                                           |
| `FieldText`       | Text in input fields                                                                 |
| `GrayText`        | Text color for disabled items (e.g. a disabled control)                              |
| `Highlight`       | Background of selected items                                                         |
| `HighlightText`   | Text color of selected items                                                         |
| `LinkText`        | Text of non-active, non-visited links                                                |
| `Mark`            | Background of text that has been specially marked (such as by the HTML mark element) |
| `MarkText`        | Text that has been specially marked (such as by the HTML mark element)               |
| `VisitedText`     | Text of visited links                                                                |

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

<iframe width="560" height="315" src="https://www.youtube.com/embed/B_n4YONte5A?si=vJOmWFjPvHmD1mRT" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

## Make textareas resize to fit their content

<div class="rich-link-card-container"><a class="rich-link-card" href="https://css-tricks.com/the-cleanest-trick-for-autogrowing-textareas/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://css-tricks.com/wp-json/social-image-generator/v1/image/324427')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">The Cleanest Trick for Autogrowing Textareas | CSS-Tricks</h1>
		<p class="rich-link-card-description">
		Earlier this year I wrote a bit about autogrowing textareas and inputs. The idea was to make a textarea more like a div so it expands in
		</p>
		<p class="rich-link-href">
		https://css-tricks.com/the-cleanest-trick-for-autogrowing-textareas/
		</p>
	</div>
</a></div>

Sync the textarea's contents to a data attribute on the wrapper:

```html
<!-- vanilla example -->
<div class="grow-wrap" data-replicated-value="">
    <textarea onInput="this.parentNode.dataset.replicatedValue = this.value"></textarea>
</div>

<!-- Svelte example -->
<div class="grow-wrap" data-replicated-value={input}>
    <textarea bind:value={input}></textarea>
</div>
```

Style the wrapper the same as the textarea, overlay them using `grid`, and hide the wrapper

```css
.grow-wrap {
    display: grid;
}

.grow-wrap textarea,
.grow-wrap::after {
    /* IMPORTANT: make sure the textarea and ::after element
       are styled exactly the same so they line up */
    white-space: pre-wrap;
    resize: none;
    overflow: hidden;
    /* make the  */
    grid-area: 1 / 1 / 2 / 2;
}

.grow-wrap::after {
    content: attr(data-replicated-value) ' ';
    visibility: hidden;
    pointer-events: none;
}
```

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
