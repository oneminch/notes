- Benefits of modular programming:
    - Code reuse
    - Namespacing
        - Avoid global namespace pollution / naming conflicts
    - Better code management

### CommonJS Modules

- In [[Node]], CommonJS modules are supported using `require()` and `module.exports` (or more succinctly `exports`).

```js
// Exporting
// ------------- 1 --------------
// same as 'module.exports.hello'
exports.hello = function () {
	console.log("hello, node!");
};

// ------------- 2 --------------
// good practice
module.exports = exports = {
	hello() {
		console.log("hello, node!");
	},
};

// ------------- 3 --------------
function hello() {
	console.log("hello, node");
}

module.exports = exports = { hello };

// ------------- 4 --------------
// Default export
module.exports = exports = function hello() {
	console.log("hello, node!");
};
```

```js
// Importing
// ------------- 1 --------------
const greet = require("./greet");

greet.hello();

// ------------- 2 --------------
const { hello } = require("./greet");

hello();
```

### ES Modules

- ES modules (`import`/`export`) are available in both the browser and Node.js. Variables, functions and classes can be exported.
- To use ES modules in the browsers, a `type` attribute of value `module` must be added to the `<script>` element.
- To use ES modules in [[Node]], either the file extension must be `.mjs` or the `package.json` must have an entry `"type": "module`.

```js
// Exporting
// ------------- 1 --------------
function hello() {
    console.log("hello, node!")
}

export const fname = "value";
export { fname as firstName };
export {
    fname,
    hello as sayHello
}

// ------------- 2 --------------
export function hello() {
    console.log("hello, node!")
}

// ------------- 3 - 1 --------------
// Default Export
export default class Person {
    constructor(name) {
        this.name = name;
    }

    hello() {
        console.log(`hello, ${this.name}!`)
    }
}

// ------------- 3 - 2 --------------
// Default Export
class Person {
    constructor(name) {
        this.name = name;
    }

    hello() {
        console.log(`hello, ${this.name}!`)
    }
}

export default Person;
// OR
export { Person as default }
```

```js
// Importing
// ------------- 1 --------------
import { fname } from "...";
import { firstName } from "...";
import { fname as firstName, hello } from "...";

// ------------- 2 --------------
import * as greet from "./greet";

console.log(greet.fname);
greet.hello();

// ------------- 3 --------------
// Importing default exports
import Person from "./person";
```

> [!note]
> Named exports can export multiple values and must use the exported name in import statements.
>
> Default exports export a single value and can be imported using any name.
