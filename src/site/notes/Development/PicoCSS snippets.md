---
{"dg-publish":true,"dg-path":"Notes/PicoCSS snippets.md","permalink":"/notes/pico-css-snippets/"}
---


# Change grid collapse breakpoint (SCSS)

```scss
@media (min-width: map-get(pico.$breakpoints, 'sm')) {
	.grid {
		grid-template-columns: repeat(auto-fit, minmax(0%, 1fr));
	}
}
```

# Reduce spacing of headings

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

# Auto width buttons with .inline

```scss
button.inline {
	display: inline;
	width: auto;
}
```
