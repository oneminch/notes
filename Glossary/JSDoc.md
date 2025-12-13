- A documentation generator for JavaScript that uses specially formatted comments to create detailed API documentation.
- Allows IDEs to show information about different symbols (e.g. functions and variables) in a codebase.
- Extremely useful when working within a team.
- Start with `/**` and end with `*/`.

```js
/**
 * Adds two numbers and returns the sum.
 * @param {number} a - The first number.
 * @param {number} b - The second number.
 * @returns {number} The sum of a and b.
 *
 * @example
 * add(1, 2);
 * // Will return 3
 */
function add(a, b) {
	return a + b;
}
```

```js
/**
 * Represents a book.
 * @typedef {Object} Book
 * @property {string} title - The title of the book.
 * @property {string} author - The author of the book.
 */

/**
 * A library catalog.
 * @type {Book[]}
 */
const catalog = [];
```
