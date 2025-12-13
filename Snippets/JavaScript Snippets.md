---
alias: JS Snippets
---

## Arrays

### Create an array of certain length

```js
// create an array of length
const list = new Array(100);

// create an array of length
// & populate it with 1's
const list = new Array(100).fill(1);
```

### Cloning an Array

```js
const cloneArr = (arr) => arr.slice(0); // Shallow Clone

const cloneArr = (arr) => [...arr]; // Shallow Clone

const cloneArr = (arr) => JSON.parse(JSON.stringify(arr)); // Deep Clone

const cloneArr = (arr) => Array.from(arr);

const cloneArr = (arr) => arr.map((x) => x);

const cloneArr = (arr) => arr.concat([]);
```

> [!warning]
>
> Be careful of values not compatible with JSON when using the `JSON.parse(JSON.stringify(anyObj))` technique. Nested objects must be serializable and deserializable via JSON. They can't be `undefined` or `null`.
>
> When in doubt, revert to `lodash.cloneDeep()` for cloning.

### Swapping values

```js
[a, c] = [c, a];
a; // -> "c"
c; // -> "a"
```

### Empty an Array

```js
const empty = (arr) => (arr.length = 0);

arr = [];
```

### Remove Duplicates

```js
// Using Set
const removeDuplicates = (arr) => {
	return [...new Set(arr)];
	// OR
	// return Array.from(new Set(arr));
};

// Using filter()
const removeDuplicates = (arr) => {
	return arr.filter((item, index, array) => array.indexOf(item) === index);
};
```

## Objects

### Cloning an Object

```js
// Shallow Cloning

let obj = { a: 1, b: 2 };
let cloneObj = { ...obj };
cloneObj.b = 4;
// obj = { a: 1, b: 2}
// cloneObj = { a: 1, b: 4 }

let anotherClone = Object.assign({}, obj);
anotherClone.b = 6;
clone.b = 4;
// obj = { a: 1, b: 2}
// anotherClone = { a: 1, b: 6 }
```

```js
// Deep Cloning

let obj = { a: 1, b: { c: 2 } };
let cloneObj = JSON.parse(JSON.stringify(obj));
cloneObj.b.c = 4;

// obj = { a: 1, b: { c: 2 }}
// cloneObj = { a: 1, b: { c: 4 } }
```

> [!warning]
>
> Be careful of values not compatible with JSON when using the `JSON.parse(JSON.stringify(anyObj))` technique. Nested objects must be serializable and deserializable via JSON. They can't be `undefined` or `null`.
>
> When in doubt, revert to `lodash.cloneDeep()` for cloning.

## Strings

### Remove non-alphanumeric characters

```js
let str = s.toLowerCase().replace(/[^a-z0-9]/gi, "");
```

### Comparison: `localeCompare()`

```js
const strings = ["ghi", "abc", "def"];

console.log(strings.sort((a, b) => a - b));
// ["ghi", "abc", "def"] ⛔

console.log(strings.sort((a, b) => a.localeCompare(b)));
// ["abc", "def", "ghi"] ✅
```

## Assorted

### Create a Hash Table of Alphabets

```js
// Create a hash table of english alphabets
const range = [...Array(26).keys()].reduce((acc, curr) => {
	acc[curr + 97] = 0;
	return acc;
}, {});
```

### String search / matching

```js
/*
  Takes in two string arguments: a string to search from and a query to search.
  Returns all the indexes the query occurs within the string as an array.
  It's case insensitive by default.
*/

const stringSearch = (str, query, caseInsensitive = true) => {
	caseInsensitive =
		typeof caseInsensitive !== "undefined" ? caseInsensitive : true;

	if (str.length === 0 || query.length === 0) {
		return [];
	}

	let indexes = [],
		i = 0,
		findIndex = -1,
		localStr = caseInsensitive ? str.toLowerCase() : str,
		localQuery = caseInsensitive ? query.toLowerCase() : query;

	while (localStr.indexOf(localQuery, i) !== -1) {
		findIndex = localStr.indexOf(localQuery, i);
		indexes.push([findIndex, findIndex + query.length]);
		i = findIndex + 1;
	}

	return indexes;
};
```

### Timer

```js
/* 
  Timer function
    Derived from JS30 course by Wes Bos -> https://javascript30.com/
*/

let bi;

const countdown_timer = (seconds) => {
	clearInterval(bi);
	const now = Date.now();
	const later = now + seconds * 1000;
	console.log(seconds);

	bi = setInterval(() => {
		const timeLeft = Math.round((later - Date.now()) / 1000);

		if (timeLeft < 0) {
			clearInterval(bi);
			return;
		}

		console.log(timeLeft);
	}, 1000);
};
```

### Typing Animation

```js
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
