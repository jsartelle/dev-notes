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
