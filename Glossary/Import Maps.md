- An import map is a [[JavaScript|JS]] object that maps a module's import name to its location of the code. This can be declared in a `<script>` tag with a `type` of `importmap`.

```html
<script type="importmap">
	{
		"imports": {
			"vue": "https://esm.sh/vue@3"
		}
	}
</script>

<div id="app">{{ message }}</div>

<script type="module">
	import { createApp } from "vue";

	createApp({
		data() {
			return {
				message: "Hello Vue!",
			};
		},
	}).mount("#app");
</script>
```
