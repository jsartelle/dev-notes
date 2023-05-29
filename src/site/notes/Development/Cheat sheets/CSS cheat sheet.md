---
{"dg-publish":true,"dg-path":"Cheat sheets/CSS cheat sheet.md","permalink":"/cheat-sheets/css-cheat-sheet/","created":"","updated":""}
---


# Selectors

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

## Attribute selectors

| Operator | Meaning                                                                           |
| -------- | --------------------------------------------------------------------------------- |
| ~=       | a whitespace-separated list of words, one of which is exactly *value*             |
| \|=      | can be exactly *value* or can begin with *value* immediately followed by a hyphen |
| ^=       | prefixed by *value*                                                               |
| $=       | suffixed by *value*                                                               |
| \*=      | contains *value*                                                                  |
| [... i]  | case insensitive                                                                  |

## :nth-child

[[Development/Clipped/Useful nth-child Recipes\|Useful nth-child Recipes]]

# Properties

## grid

- Use `order` to rearrange grid items
- Use [[Development/Cheat sheets/CSS cheat sheet#gap\|#gap]] to create gutters between grid tracks

### Grid container properties

#### grid-template-columns, grid-template-rows

- Defines the count and size of explicit grid tracks (rows or columns)
- Use `repeat` to save typing

```css
grid-template-columns: 50% 50%;
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

- Use the `fr` unit to represent one "part" of the available space
- If some columns are defined with lengths or percentages, `fr` divides the remaining space

```css
grid-template-columns: 1fr 4fr; /* same as 20% 80% */
grid-template-rows: 50px repeat(3, 1fr) 50px;
```

##### auto-fill

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

##### auto-fit

- Use `auto-fit` with `minmax` to make the tracks expand to fit any leftover space

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

#### grid-template

- Shorthand for the above: `rows / columns`

#### grid-auto-flow

- Controls the direction (`row` or `column`) that implicit grid tracks are created in (default: `row`)
- Add `dense` to "fill in" holes earlier in the grid (this may cause items to display out of order)

#### grid-auto-rows, grid-auto-columns

- By default implicit rows/columns are sized to fit their content, this property lets you give them an explicit size

```css
grid-auto-rows: 100px;
grid-auto-columns: minmax(100px, auto);
```

#### align-items, justify-content

- Works like in flexbox, except with `start` and `end` rather than `flex-start` and `flex-end`
{ #0c043d}

    - Aligns each item within its grid area (could span many cells)

### Grid item properties

#### grid-row-start, grid-column-start, grid-row-end, grid-column-end

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

#### grid-row, grid-column

- Shorthand for the above: `start / end`

#### grid-area

- Shorthand for `grid-row-start / grid-column-start / grid-row-end / grid-column-end`

#### align-self


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/cheat-sheets/css-cheat-sheet/#0c043d" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">



- Works like in flexbox, except with `start` and `end` rather than `flex-start` and `flex-end` 

</div></div>


#### position: absolute

- Grid items with `position: absolute` will take their grid area as their containing block if they have one, or the entire grid if they don't
    - Make sure the grid container has `position: relative`

<div class="grid-example" style="position: relative; grid-template-columns: repeat(3, 1fr);">
    <div></div>
    <div class="active"></div>
    <div></div>
    <div class="abs-fill" style="background-color: deepskyblue; grid-column: 2 / 3; left: 50px; top: 20px;"></div>
</div>

#### display: contents

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

### See also

[[Development/Clipped/Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers\|Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers]]

## gap

- Sets spacing between tracks in flex or grid views
    - Unlike margins, `gap` doesn't apply to the outer edges
- First value is gap between rows, second is gap between columns
    - If only one value is given, it applies to both
    - There are also separate `row-gap` / `column-gap` properties

```css
gap: 10px 5px;
```

## mask

- Requires `-webkit-` prefix on Chromium browsers
- Useful for [changing the color of SVG background images](https://codepen.io/noahblon/post/coloring-svgs-in-css-background-images)

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
- use `contain: content` for elements such as articles that are independent from the rest of the page

## content-visibility

- Controls whether the browser renders the element's contents
- `hidden`: the element is never rendered
- `auto`: the element is only rendered if it is "relevant to the user" - in or near the viewport, focused, selected, or in the top layer

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

## cross-fade

### Specification syntax

- Supports any number of images, with later images on top
- If any opacity percentages are left out, it will evenly distribute them to reach 100%

```css
cross-fade(url(white.png), url(black.png)) /* 50% white 50% black */
cross-fade(url(white.png), url(black.png) 75%) /* 25% white 75% black */
cross-fade(url(white.png), url(black.png) 100%) /* 100% black */
```

### Implemented syntax

> [!warning]
> Safari 16 does not correctly handle `linear-gradient` inside `-webkit-cross-fade`!

- Only supports two images, opacity applies to the first image

```css
-webkit-cross-fade(url(white.png), url(black.png), 0%) /* fully black */
-webkit-cross-fade(url(white.png), url(black.png), 100%) /* fully white */
```

# At-rules

## @property

- Allows you to define CSS custom properties with control over data types, inheritance, and `initial` value

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
    - `<length-percentage>`
    - `<color>`
    - `<image>`
    - `<url>`
    - `<integer>`
    - `<angle>`
    - `<time>`
    - `<resolution>`
    - `<transform-function>`
    - `<custom-ident>`
    - `<transform-list>`

- Examples:

```css
syntax: "<color>";
syntax: "<length> | <percentage>";
syntax: "small | medium | large"; /* custom values */
syntax: "*"; /* any value */
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

## Transition from 0 to auto

- Place the element in a container with this styling

```css
display: grid;
grid-template-rows: 0fr;
transition: grid-template-rows;
```

- To transition to auto height, change `grid-template-rows` to `1fr`

<iframe width="560" height="315" src="https://www.youtube.com/embed/B_n4YONte5A" title="YouTube video player" frameborder="0" allow="encrypted-media; picture-in-picture; web-share" allowfullscreen></iframe>

# See also

- [[Development/Cheat sheets/Sass cheat sheet\|Sass cheat sheet]]
- [Modern CSS Upgrades To Improve Accessibility | Modern CSS Solutions](https://moderncss.dev/modern-css-upgrades-to-improve-accessibility/)
