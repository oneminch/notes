- Hoisting is the process where variables, functions and class declarations (_not assignments_) are moved to the top of their scope before they are executed.
- Functions can be safely used in code before their declaration.

```js
fn();

function fn() {
	/* code */
}
```

- In the case of [[variables]], only declarations are hoisted, not initializations / assignments; the interpreter acknowledges the existence of the variable but not its assigned value.

```js
console.log(foo); // prints 'undefined' from hoisted var declaration (not 6)
var foo = 6;
console.log(foo); // prints 6

console.log(bar); // prints error - ReferenceError
let bar = 6;
console.log(bar); // prints 6
```

- With `let` and `const`, the order in which code is _executed_ is what matters, not the order in which it is written in the source.
- Function _expressions_ and class _expressions_ are not hoisted.
