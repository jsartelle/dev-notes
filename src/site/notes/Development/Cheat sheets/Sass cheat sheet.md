---
{"dg-publish":true,"dg-path":"Cheat sheets/Sass cheat sheet.md","permalink":"/cheat-sheets/sass-cheat-sheet/","tags":["language/css"]}
---


- Interpolate Sass variables into CSS custom properties using `#{}`

# Math

- No need to use `calc` for expressions that are based on constants or Sass variables
- `/` is deprecated for division - use `math.div()` instead

```scss
@use 'sass:math';
$button-height: 30px;
button {
    height: $button-height;
    width: $button-height * 2;
    border-radius: math.div($button-height, 2);
}
```

# Property nesting

- Group properties that share a common prefix
- Can be used instead of shorthands to make your stylesheet more readable

```scss
animation: {
  duration: 0.5s;
  timing-function: linear;
  iteration-count: infinite;
  direction: alternate;
  fill-mode: both;
  name: bounce;
}
```

# Reusing Rules

## @extend

- Lets you inherit rules from other classes and [[Development/Cheat sheets/Sass cheat sheet#Placeholder selectors\|placeholder selectors]]

```scss
.error {
    background-color: red;
    &--serious {
        @extend .error;
    }
}
```

- Compiles to a compound selector

```css
.error, .error--serious {
    background-color: red;
}
```

> [!warning]
> `@extend` cannot be used across at-rule boundaries! Use [[Development/Cheat sheets/Sass cheat sheet#@mixin and @include\|#@mixin and @include]] instead.

### Placeholder selectors

- Selectors that aren't included in the CSS, and only exist to be extended

```scss
%danger {
    background-color: red;
}

.error {
    @extend %danger;
}
```

## @mixin and @include

- Creates blocks of rules that can be reused, and are emitted separately each time

```scss
@mixin danger {
    background-color: red;
}

.error {
    @include danger;

    &--serious {
        @include danger;
    }
}
```

- Compiles to

```css
.error {
    background-color: red;
}

.error--serious {
    background-color: red;
}
```

# See also

- [[Development/Cheat sheets/CSS cheat sheet\|CSS cheat sheet]]
