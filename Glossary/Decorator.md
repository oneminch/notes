- A decorator is a function that takes another function as an argument and changes its behavior.
- The [[JavaScript]] code below creates the `cachingDecorator` that adds caching capabilities to a function:

```js
let worker = {
	slow(min, max) {
		return min + max;
	},
};

function anotherSlow(x) {
	return x ** x;
}

// simple hasing function
function hash() {
	return [].join.call(arguments);
}

function cachingDecorator(func, hash) {
	let cache = new Map();
	return function () {
		let key = hash(arguments); // (*)
		if (cache.has(key)) {
			return cache.get(key);
		}

		let result = func.call(this, ...arguments); // (**)

		cache.set(key, result);
		return result;
	};
}

worker.slow = cachingDecorator(worker.slow, hash);

console.log(worker.slow(3, 5)); // works
console.log("Cached: " + worker.slow(3, 5)); // same (cached)

anotherSlow = cachingDecorator(anotherSlow, hash);

console.log(anotherSlow(5)); // works
console.log("Cached: " + anotherSlow(5)); // same (cached)
```

- **Credit**: [JavaScript.Info](https://javascript.info/call-apply-decorators)
