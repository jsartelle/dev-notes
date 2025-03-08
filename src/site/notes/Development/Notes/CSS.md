`==, as it makes the browser download CSS sequentially and slows down rendering. Instead, link the stylesheets separately in your HTML, or use a bundler to combine them into one stylesheet.

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
- You can also specify a layer name when using `