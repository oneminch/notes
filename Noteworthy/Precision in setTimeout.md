- Nested `setTimeout` allows more precise interval executions than `setInterval`.

```js
// Typing Animation
const elt = document.querySelector("h1");
const str = "Hello, World!";

let i = 0,
	j = str.length;

let timerId = setTimeout(function type() {
	if (i < str.length) {
		elt.textContent += str[i];
		i += 1;
	} else if (j >= 0) {
		elt.textContent = str.slice(0, j);
		j -= 1;
	}

	if (j >= 0 || i < str.length) {
		timerId = setTimeout(type, 200);
	}
}, 200);
```
