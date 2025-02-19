---
{"dg-publish":true,"dg-path":"Notes/PicoCSS.md","permalink":"/notes/pico-css/","tags":["language/css"]}
---


# Installation with frameworks

> [!tip]
> Directions are for SvelteKit, but Next.js is similar

- Install dependencies

```shell
yarn add @picocss/pico
```

Svelte 4:

```shell
npx svelte-add@latest scss
```

Svelte 5:

```shell
yarn add --dev sass-embedded
```

- Add `id="root"` to the `<div>` container in `app.html`
    - not needed for Next.js

- Create `src/lib/app.scss` (`src/app.scss` for Next.js) with this content
    - remove the `$semantic-root-element` line for Next.js

```scss
@use '@picocss/pico/scss/pico' with (
  $semantic-root-element: '#root',
  $enable-semantic-container: true,
  $enable-responsive-spacings: true
);
```

- In the root layout, import `$lib/app.scss`

## Cascade layer

- To load Pico on a cascade layer, add the code above to `src/pico.scss`, and add this to `app.scss` instead

```scss
@use 'sass:meta';

@layer pico {
	@include meta.load-css('pico');
}
```

- Anything you write in `pico.scss` will be on the `pico` layer, and will be overridden by anything in `app.scss` or components

# Colors

- to change the color scheme:

```scss
@use "pico" with (
  $theme-color: "purple"
);
```

Color choices:

- `amber`
- `azure`
- `blue`
- `cyan`
- `fuchsia`
- `green`
- `grey`
- `indigo`
- `jade`
- `lime`
- `orange`
- `pink`
- `pumpkin`
- `purple`
- `red`
- `sand`
- `slate`
- `violet`
- `yellow`
- `zinc`

# Snippets

## Change grid collapse breakpoint (SCSS)

```scss
@media (min-width: map-get(pico.$breakpoints, 'sm')) {
	.grid {
		grid-template-columns: repeat(auto-fit, minmax(0%, 1fr));
	}
}
```

## Reduce spacing of headings

```css
h1,
h2,
h3,
h4,
h5,
h6 {
	--typography-spacing-vertical: 1.5rem;
}
```

## Auto width buttons with .inline

```scss
button.inline {
	display: inline;
	width: auto;
}
```
