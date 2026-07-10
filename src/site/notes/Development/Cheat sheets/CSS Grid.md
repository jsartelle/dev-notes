---
{"dg-publish":true,"dg-path":"Cheat sheets/CSS Grid.md","permalink":"/cheat-sheets/css-grid/","contentClasses":"css-cheat-sheet","tags":["language/css"],"dg-note-properties":{"created":"2026-01-30T08:52:56-06:00","modified":"2026-01-30T09:00:35-06:00","tags":["language/css"],"cssclasses":["css-cheat-sheet"]}}
---


# General

- Use `order` to rearrange grid items
- Use [[Development/Cheat sheets/CSS#gap\|gap]] to create gutters between grid tracks
- [[Development/Clipped/Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers\|Exploring CSS Grid’s Implicit Grid and Auto-Placement Powers]]

# Grid container properties

## grid-template-columns, grid-template-rows

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

- Use `auto` for automatic sizing, or `none` to remove the explicit grid

### fr

- The `fr` unit represents one "part" of the available space
- Unlike percentages, `fr` divides up **extra** space - columns won't overflow, even if that means breaking the proportions given

```css
grid-template-columns: 1fr 3fr;
```

<div class="grid-example" style="grid-template-columns: 1fr 3fr;">
    <div style="white-space: nowrap;">This is a long sentence that will overflow this column if allowed</div>
    <div></div>
</div>

```css
grid-template-columns: 25% 75%;
```

<div class="grid-example" style="grid-template-columns: 25% 75%;">
    <div style="white-space: nowrap;">This is a long sentence that will overflow this column if allowed</div>
    <div></div>
</div>

- `fr` also ignores [[Development/Cheat sheets/CSS#gap\|gap]], while percentages don't (since they're based on the total area of the grid container)

<div class="grid-example" style="grid-template-columns: 1fr 3fr; gap: 20px;">
    <div>1fr</div>
    <div>3fr</div>
</div>

<div class="grid-example" style="grid-template-columns: 25% 75%; gap: 20px;">
    <div>25%</div>
    <div>75% - overflowing!</div>
</div>

### minmax

- Item size will be >= min and <= max
- use `minmax(0, 1fr)` to keep all rows/columns the same size, and keep them from overflowing (see [[Development/Cheat sheets/CSS#Prevent flex and grid items from overflowing container\|Prevent flex and grid items from overflowing container]])

### auto-fill

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
</div>

### auto-fit

- Use `auto-fit` with [[Development/Cheat sheets/CSS Grid#minmax\|#minmax]] to make the tracks expand to fit any leftover space

```css
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

<div class="grid-example" style="grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); background-color: var(--background-secondary);">
    <div></div>
    <div></div>
    <div></div>
    <div></div>
    <div></div>
</div>

### Named lines

- You can name grid lines to make them easier to reference with [[Development/Cheat sheets/CSS Grid#grid-row-start, grid-column-start, grid-row-end, grid-column-end\|#grid-row-start, grid-column-start, grid-row-end, grid-column-end]]
    - You can give the same line multiple names separated by spaces
- If you name lines with `-start` and `-end`, the browser will generate an implicit [[Development/Cheat sheets/CSS Grid#grid-template-areas\|named area]] between them (in the below example, `sidebar` and `article`)
    - The opposite is true: if you create a grid area named `article`, it will generate named lines in each direction called `article-start` and `article-end`

```css
main {
    grid-template-columns:
        [sidebar-start] 200px
        [sidebar-end article-start] 1fr [article-end];
}

.sidebar {
    grid-column: sidebar-start / sidebar-end;
}

.article {
    grid-column: article-start / article-end;
}
```

<div class="grid-example named-lines">
    <div style="grid-column: sidebar-start / sidebar-end">
        <span>sidebar-start</span>
    </div>
    <div style="grid-column: article-start / article-end">
       <span>sidebar-end<br>article-start</span>
        <span>article-end</span>
    </div>
</div>

## grid-template-areas

- Lets you name certain grid areas, to make it easier to assign elements to them using [[Development/Cheat sheets/CSS Grid#grid-area\|#grid-area]]
    - Also lets you change the layout without having to change styles for all the child elements
- Each string is a row, and each space-separated token within the string is a column
    - strings don't need to be on separate lines (ie. you could write `"a a a" "b c c" "b c c"`), but putting them on separate lines makes them more readable
- Areas must be rectangular (ex. you can't do `"a a b" "a b b"`)
- [[Development/Cheat sheets/CSS Grid#Named lines\|#Named lines]] named with `-start` and `-end` will generate an implicit named area
    - Conversely, named areas will generate implicit named lines with `-start` and `-end` appended
- Named areas created this way can't overlap, but you can create [[Development/Cheat sheets/CSS Grid#Named lines\|#Named lines]] with `-start` and `-end` suffixes to create overlapping named areas

```css
.grid {
    display: grid;
    grid-template-areas:
        "a a a"
        "b c c"
        "b c c";
}
```

<div class="grid-example grid-areas">
    <div style="grid-area: a">a</div>
    <div style="grid-area: b">b</div>
    <div style="grid-area: c">c</div>
</div>

- Use one or more dots to leave an empty space

```css
grid-template-areas: 
    ". a a"
    "b c c"
    "b c c";
```

<div class="grid-example grid-areas empty-space">
    <div style="grid-area: a">a</div>
    <div style="grid-area: b">b</div>
    <div style="grid-area: c">c</div>
</div>

- use [[Development/Cheat sheets/CSS#@media (media queries)\|media queries]] to make area layouts responsive

## grid-template

- Shorthand for [[Development/Cheat sheets/CSS Grid#grid-template-columns, grid-template-rows\|#grid-template-columns, grid-template-rows]]

```css
grid-template: repeat(auto-fill, 200px) / repeat(2, 1fr);
```

- Can also be a shorthand for [[Development/Cheat sheets/CSS Grid#grid-template-areas\|#grid-template-areas]] and row/column sizes
    - Row heights go after each row, column widths go at the end separated by a `/`

```css
grid-template:
    "a a a" 100px 
    "b c c" 50px
    "b c c" 50px / repeat(3, 1fr);
```

## grid-auto-flow

- Controls the direction (`row` or `column`) that implicit grid tracks are created in (default: `row`)
- Add `dense` to "fill in" holes earlier in the grid - this may cause items to display out of order

## grid-auto-rows, grid-auto-columns

- By default, implicit rows/columns are sized to fit their content, this property lets you give them an explicit size

```css
grid-auto-rows: 100px;
grid-auto-columns: minmax(100px, auto);
```

# Grid item properties

## grid-row-start, grid-column-start, grid-row-end, grid-column-end

- Set which grid **lines** (not tracks!) an element's starting or ending edges touch (1-indexed)
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

- Can also use [[Development/Cheat sheets/CSS Grid#Named lines\|#Named lines]] (don't put the name in quotes)
- If the specified lines are outside the bounds of the [[Development/Cheat sheets/CSS Grid#grid-template\|#grid-template]], the browser will generate implicit grid tracks for the item
    - You can use this and [[Development/Cheat sheets/CSS Grid#grid-auto-flow\|#grid-auto-flow]] to accomplish some layouts without defining a `grid-template` at all

## grid-row, grid-column

- Shorthand for the above: `start / end`

## grid-area

- Shorthand for `grid-row / grid-column`, or `grid-row-start / grid-column-start / grid-row-end / grid-column-end`
- Can also specify a named area from [[Development/Cheat sheets/CSS Grid#grid-template-areas\|#grid-template-areas]] (not in quotes)

## position: absolute

- Grid items with `position: absolute` will take their grid area as their containing block if they have one, or the entire grid if they don't
    - Make sure the grid container has `position: relative`
- In the example below, the highlighted block has `grid-column: 2 / 3` set

<div class="grid-example" style="position: relative; grid-template-columns: repeat(3, 1fr);">
    <div></div>
    <div class="active"></div>
    <div></div>
    <div class="abs-fill" style="background-color: deepskyblue; grid-column: 2 / 3; left: 50px; top: 20px;"></div>
</div>

## display: contents

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
