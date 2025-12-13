- Currying is a [[Functional Programming|FP]] technique that makes use of [[higher-order functions]].
- It is a technique for transforming functions where a function that accepts multiple arguments is broken up into a series of functions that each accept one argument.
    - It transforms `f(a, b, c)` to `f(a)(b)(c)`.

```js
function add(x, y) {
	return x + y;
}

function add(x) {
	return function (y) {
		return x + y;
	};
}
```
