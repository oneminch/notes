---
alias: JS
---

## Introduction

- JavaScript is
    - Lightweight
    - [[Interpreted Language|Interpreted]] & [[Dynamically-Typed Language]]
- Most modern JavaScript interpreters use [[Just-In-Time Compilation]] to improve performance.
- It runs on any device that has a special program called _the JavaScript engine_:
    - SpiderMonkey - Firefox
    - V8 - Chromium
    - Javascript Core - Safari
- The JavaScript runtime is single threaded; it can only run one function at a time.
- Everything in JS is an object and can be stored in a variable.
- A single `<script>` tag can't have a `src` attribute and content inside.
    - The `type` and `language` attributes are no longer required.

## Fundamentals

- **[[Variables]]**
    - are _containers_ for storing values.
    - Due to design flaws with `var`, it's recommended to use modern versions `let`.
        - `var` causes confusion because it allows [[hoisting]] and redeclaration of variables.
            - Unlike `let`, `var` has no block scope; it creates either function-scoped or global-scoped variables.
    - names can start with an underscore (`_`) or a dollar sign (`$`), in addition to letters.
- **Constants**
    - are like variables except that:
        - they must be initialized upon declaration.
        - after initializing, a new value can't be assigned to them.

```javascript
let a; // ✅ valid, no error

const b; // ⛔ will throw an error

let c;
c = 1; // ✅ valid, no error

const d;
d = 1; // ⛔ will throw an error
```

- For reference types like objects, the content of the value that a constant names can be changed.

```javascript
const person = { name: "John Doe" };
person.name = "Jane Doe"; // ✅ valid
```

- **Comments**
    - Single line: `// comment`
    - Multi-line: `/* comment */`
    - Nested comments are not supported using the multi-line syntax.

### Script Loading Techniques

- Script execution blocks page rendering.
- `async` and `defer` allow scripts to be downloaded in a separate thread without interfering with the page loading process.
- `async` execute as soon as the download is complete. They should be used to load independent scripts and background scripts that don't interfere with rendering.
    - e.g. loading data that could be used later on.
- `defer` is similar to `async` but script is executed after document is done being parsed.
    - Scripts will run in the order they appear in the page; they get executed as soon as the script and content have finished downloading.
- It's important to use the appropriate attributes and context to load scripts.

> [!quote] Techniques
> ![script-loading.jpg](/assets/images/js.script-loading.jpg)
>
> **Source**: MDN

### Operators

**Arithmetic**

- `+`, `-`, `*`, `/`, `%`, `**` (exponent)
    - `a**x` is equivalent to `Math.pow(a, x)` ($a^x$).

**Increment / Decrement**

- `++` & `--`
- These can't be applied directly to a number, but the variable holding the number.

**Comparison**

- `==`, `!=`, `===`, `!==`, `<`, `>`, `<=`, `>=`

**Logical**

- `&&` (and), `||` (or), `!` (not / negation)
- `&&` - finds the first '==falsy==' value; has higher precedence than `||`.
- `||` - finds the first 'truthy' value.

| Logical Assignment Operator | Equivalent to    | Assigns `b` to `a` Only If `a` is |
| --------------------------- | ---------------- | --------------------------------- |
| `a \|\|= b`                 | `a \|\| (a = b)` | Falsy                             |
| `a &&= b`                   | `a && (a = b)`   | Truthy                            |
| `a ??= b`                   | `a ?? (a = b)`   | Nullish                           |

**Bitwise**

- `&` (AND), `|` (OR), `~` (NOT), `^` (XOR), ...

**Assignment**

- Chained assignments are evaluated from right to left.

```js
let a, b, c;

a = b = c = 2 + 2;

// evaluates to

c = 2 + 2;
b = c;
a = c;
```

- Shortcut operators like `+=` and `%=` are called ==augmented assignment operators==.

- **Learn more** - [Operator Precedence Table](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence#table)

### Garbage Collection / Memory Management

- Done automatically in JS.
- Reachable objects are retained in memory.
    - [Read more 📄](https://javascript.info/garbage-collection)

## Primitives

- [[Immutable]] data types.
- With the exception of `null` and `undefined`, primitives are treated like objects; they have object equivalent wrappers, and hence inherited methods.
- Object wrappers that provide certain functionality are created on demand and then destroyed.

> [!example]
> When the `toUpperCase()` function is called on a string, a special object wrapper with the string value is created. After the method runs and returns, the wrapper is destroyed.
>
> Due to the lack of such wrapper objects, `null` & `undefined` are considered the most primitive.

- To keep primitives as lightweight as possible, constructors (`String` / `Number` / `Boolean`) should only be reserved for internal use only; using those functions without the `new` keyword is fine.

### Numbers

- **Integers** are floating-point numbers without a fraction; either negative or positive.
- **Floats** have decimal points and decimal places, for example 2.5, and 9.77.
- **Doubles** are a type of float with greater precision than standard floats.

> [!note]
> JavaScript primarily has one data type for dealing with integers and decimals - `Number`. But, it also has a second number type, `BigInt` which is used for _really large_ integers.

- The range for the "normal" number types is between $-(2^{53} - 1)$ and $2^{53} - 1$.
- `BigInt` values can be created by appending `n` to the end of an integer.
    - [Read more 📄](https://javascript.info/bigint)

```js
const bigInteger = 012345678901234567890123456789n;
const sameBigInteger = BigInt("012345678901234567890123456789");
```

> To call a method directly on a number, the number must either be wrapped in parenthesis or be followed by two dots: `..` (e.g. `2..toString()`).

- Special numeric values also exist: `Infinity`, `-Infinity`, `NaN` (which represents a computational error).
    - The value of `NaN` is unique; it is not equal to anything, not even to itself.
    - Stored in 64-bit format; if a number overflows this storage, it becomes `Infinity`.

```js
let num = 15 / 0; // Infinity
let num = -15 / 0; // -Infinity
```

- Numbers can start with certain characters that represent the numeral system they belong to:
    - Binary -> `0b`; e.g. `0b11001`
    - Hexadecimal -> `0x`; e.g. `0x19`
    - Octal -> `0o`; e.g. `0o31`

- `num.toString(base)` can be used to "convert" a number to the given base numeral system and return a string representation; `2` <= `base` <= `36`, default value is `10`.

```js
// Number conversion
(25).toString(2); // -> "11001"
(25).toString(8); // -> "31"
(25).toString(16); // -> "19"
(25).toString(36); // -> "p"
```

- To increase readability, numbers can be separated with `_`; trailing zeros can be shortened using exponentiation.

```js
// Ways to write numbers
let million = 1_000_000;
let billion = 1e9;
let micro = 1e-6;
```

#### Numeric Conversions

- Converting String to Number: `Number("25")`
- Converting Number to String: `(25).toString()`

| **Original** | **Converted**                                     |
| ------------ | ------------------------------------------------- |
| `undefined`  | `NaN`                                             |
| `null`       | `0`                                               |
| `string`     | whitespaces trimmed; empty -> `0`; error -> `NaN` |

#### Numeric Methods

- Numbers inherit methods from the `Number.prototype` object.
    - e.g. `parseInt()`, `parseFloat()`, `toFixed()`, `isNaN()`, `isFinite()` and `toString()`
- `parseInt()` & `parseFloat()` read a number left to right from a string until they can't:

```js
parseInt("50px"); // 50
parseFloat("2.5em"); // 2.5

parseInt("2.5"); // 2
parseFloat("2.5.5"); // 2.5
```

#### Precision Issues

- Numbers are stored in binary form; simple decimal fractions translate to unending fractions in their binary form. This causes loss of precision.
- There is no possible way to store an exact fraction like `0.2`.
- A single number might not be obvious because they are usually rounded to the nearest number; it becomes evident that precision loss exists when an operation is performed.
- The most reliable method to resolve this issue is to use `toFixed()` to round the result:

```js
(0.1).toFixed(20); // -> 0.10000000000000000555

let s = 0.7 + 0.2; // -> 0.8999999999999999

+s.toFixed(2); // -> 0.9
```

**Other weird cases**:

- Two zeros exist: `0` and `-0`

```js
9999999999999999; // -> 10000000000000000

-0 === 0; // -> true
Object.is(-0, 0); // -> false

NaN === NaN; // -> false
Object.is(NaN, NaN); // -> true
```

### Strings

- Expressions can be included in template literals.
- Template literals respect line breaks in strings. When writing normal strings, this can be achieved using `"\n"`.

```js
const str1 = `Hello, World!
    This is a string.`;

// 👆🏾 is equivalent to 👇🏾

const str2 = "Hello, World!\nThis is a string.";
```

- Special characters like `\n` count towards the length of a string: `"Hi\n".length` -> `3`
- Strings inherit methods from the `String.prototype` object. e.g. `substring()`, `indexOf()`, `concat()` and `toString()`.
    - Using these methods creates new strings; it doesn't modify existing ones. ([[Immutable]])

#### Comparison

- When comparing values of different types, JavaScript converts the values to numbers.

```js
"2" > 1; // true, "2" becomes 2
```

- The correct way to compare strings: `localeCompare()`

#### Concatenation

```js
alert(2 + 2 + "1"); // "41" and not "221"

alert("1" + 2 + 2); // "122" and not "14"
```

### Boolean

- `true` / `false`
- **Falsy values** evaluate to false when executed in a boolean operation.
    - `false`, `null`, `undefined`, `""` (empty string), `0`, `NaN`
- **Truthy values** evaluate to true when executed in a boolean operation.
    - All non-falsy values and objects are truthy.
    - A non-empty string always evaluates to true. e.g. `"0"`

### `undefined`

- Represents an unassigned value.
- Doesn't have a wrapper object.

### `null`

- `typeof null` -> `"object"`
- Represents the intentional absence of any object value.
- Doesn't have a wrapper object.

### Symbols

- Used to create unique identifiers for objects.
- Can be used to create hidden object properties.
- Ignored by `Object.keys()` & `for...in` loops.

```js
let id = Symbol("id");	// symbol with optional description "id"

let obj = {
  [id]: 1
  name: "Jane"
}
```

### Type Casting vs Coercion

> [!important]
>
> - **Coercion** is the automatic or implicit conversion of a type.
>     - e.g. Adding a number to a string will result in the number being coerced into a string and concatenated.
> - Unlike coercion, **Type Casting** is an explicit and deliberate operation.
>     - Typically done using functions like `Number()`, `String()` or `Boolean()`.

## Objects

- Mutable, but can be made [[Immutable]] with `Object.freeze(obj)`.
- Unlike primitives, objects are stored and copied by reference; a variable assigned to an object contains its address in memory.

- Two objects are equal only if they reference the same object:

```js
let a = {};
let b = a;
let c = {};

a == b; // true
a === b; // true
a == c; // false
```

- Multi-word property names must be quoted.

- There are no restrictions on object property keys, even reserved words (like `for` and `let`) are allowed.
- Only strings and symbols can be used as object keys; other types are converted to strings. e.g. `0` -> `"0"`

- Reading a non-existing property just returns `undefined`.

```js
// object literal
const person = {
	name: "John",
	greet: function () {
		console.log(`Hi, my name is ${this.name}`);
	},
	// OR shortly,
	// greet() {
	//   console.log(`Hi, my name is ${this.name}`);
	// }
};

console.log(user.newProp === undefined); // true
console.log("newProp" in person); // false
```

- Using `in` results in more accurate property existence checks.
    - `undefined` equality fails when a property exists but has an explicit `undefined` value: `obj.key = undefined`

- Object keys that are integers are sorted; other types follow their creation order.

- ==Optional chaining== (`?.`) can be used to solve the "non-existing property" problem. It does that by stopping the evaluation if the value before `?.` is `undefined` or `null` and returns `undefined`.
    - [Read more 📄](https://javascript.info/optional-chaining)

```js
let p = {};

p.name.first; // -> TypeError; p.name is undefined

p?.name; // -> undefined
p?.name?.first; // -> undefined
```

- When assigning object property values using variables, if the key-value names are identical, we can use the shorthand method.

```js
function newPerson(name, age) {
	return {
		name, // name: name
		age, // age: age
	};
}
```

### Arrays

> `typeof []` -> `"object"`
>
> `Array.isArray([])` -> `true`

- Store ordered collections.
- Trailing commas are allowed.
- The `length` property is writable; modifying it is an irreversible process. Decreasing it truncates the array. It can also be set to `0` to clear an array.
- Arrays shouldn't be compared using `==`.
- The `delete` operator on an array doesn't shift items after deletion; length remains the same.

> [!note]
> During an operation, if the index of an array is not available, it will return `undefined`; there are no "index out of range" exceptions.

#### Array Methods

- `pop()` and `unshift()` methods can add multiple values at once.
- Methods that work with the end of an array (`pop()` & `push()`) are faster than ones that work with the beginning (`shift()` & `unshift()`).
- `Array.prototype.toString()` has the same result as `Array.prototype.join()`.

```js
[] + 1; // -> "1"
[1] + 2; // -> "12"
[1, 2] + 3; // -> "1,23"
```

- **Add / Remove Items**
    - `arr1.concat(arr2)`
    - `arr.fill(value, start, end)`
    - `arr.slice()`
    - `arr.splice()` - insert, remove and replace elements in an array.
- **Iterate**
    - `arr.entries()`
    - `arr.forEach(elt, index, array)`
    - `arr.keys()`
    - `arr.values()`
- **Search / Lookup**
    - `arr.at(index)`
    - `arr.filter()`
    - `arr.find()`, `arr.findIndex()`, `arr.findLast()`, `arr.findLastIndex()`,
    - `arr.includes(value)`
    - `arr.indexOf(value)`, `arr.lastIndexOf(value)`
- **Transform**
    - `arr.map()`
    - `arr.reduce(reducerFn, initValue = arr.at(0))`
    - `arr.reduceRight(reducerFn, initValue = arr.at(-1))`
    - `arr.sort()` / `arr.reverse()`
    - `arr.splice(start[, deleteCount, ...newItems])` / `arr.toSpliced()`
    - `arr.split()` / `arr.join()`
    - `arr.toSorted()` / `arr.toReversed()`

> [!note]
> A reducer function (in `reduce()` and `reduceRight()` methods) is called on each element with the return value of the calculation from the previous element. Final output is a single value.

```js
const strArr = ["H", "e", "l", "l", "o", "!"];

const forwardStr = strArr.reduce((accumulator, el) => (accumulator += el), "");

const reverseStr = strArr.reduceRight(
	(accumulator, el) => (accumulator += el),
	"",
);

// forwardStr: "Hello!"
// reverseStr: "!olleH"
```

- **Static Methods**
    - `Array.from(arrayLike, mapFn)`
    - `Array.isArray()`
    - `Array.of()`

#### Sparse Arrays

- Sparse arrays are arrays that contain 'empty slots'.
- They can be created in several ways:

```js
const x = new Array(5);

const y = [1, 2, , , 5];

const z = [1, 2];
z[4] = 5;
z.length = 10;

const w = [1, 2, 3, 4, 5];
delete w[2];
```

### Iterables

- _Iterable objects_ implement the `Symbol.iterator` method. It allows us to make any object loopable or "iterable" in a `for...of` loop.
- _Array-likes_ have indices and a `length`.
- `Array.from()` creates a real array from an array-like or an iterable value.
- [Read more 📄](https://javascript.info/iterable)

### Maps

- A collection of key-value pairs (like an object), but insertion order is remembered, and either key or value can be of _any_ type: object and primitive.
- Setting and getting values is and should be done through `set()` and `get()` methods.
    - `set()` is chainable.
- It uses a similar approach to strict equality to compare keys, but `NaN` is considered equal to `NaN`.
- can be looped using `for...of` and `forEach(value, key, map)`.
- Other methods include `has(key)`, `delete(key)`, and `clear()`.
    - The `keys()`, `values()`, and `entries()` methods can be used for iterating; `entries()` is the default used in a `for...of` loop.
- Maps also have a `size` attribute that returns the number of pairs.

```js
let mapOne = new Map();
mapOne.set(1, "one").set("2", "two").set(true, "three");

let mapTwo = new Map([
	[1, "one"],
	["2", "two"],
	[true, "three"],
]);

let mapThree = new Map(
	Object.entries({
		name: "Jane",
		age: 35,
	}),
);
```

#### WeakMaps

- Maps whose keys can only be objects, not primitives.
- Unlike Maps, it doesn't prevent keys from being garbage-collected; they are "weakly-held".
- No support for iterations.
- [Read more 📄](https://javascript.info/weakmap-weakset)

### Sets

- A collection of values (like an array), where duplicates are not allowed.
- Share similar functionality and methods with Maps.
- can be looped using `for...of` and `forEach`.
- Set methods include `has(value)`, `add(value)`, `delete(value)`, `clear()`.
    - The `keys()`, `values()`, and `entries()` methods can be used for iterating; `entries()` is the default used in a `for...of` loop.
- Sets have a `size` attribute that returns the number of elements.

```js
let set = new Set();

let john = { name: "John" };
let pete = { name: "Pete" };
let mary = { name: "Mary" };

// visits, some users come multiple times
set.add(a);
set.add(b);
set.add(c);
set.add(b);
set.add(a);
```

#### WeakSet

- Behave similar to WeakMaps: only object values allowed.
- No support for iterations.
- [Read more 📄](https://javascript.info/weakmap-weakset)

### Getters & Setters

- Object properties are of two kinds: data properties and accessor properties.
- Accessor properties are functions that look like regular properties.
    - They are represented by _getter_ and _setter_ methods that execute on getting and setting a value.
    - They are _not called_ like a method but _read_ as a property.

```js
let person = {
	firstName: "Jane",
	lastName: "Doe",

	get fullName() {
		return `${this.firstName} ${this.lastName}`;
	},

	set fullName(value) {
		[this.firstName, this.lastName] = value.split(" ");
	},
};

console.log(person.fullName); // Jane Doe

person.fullName = "John Smith";
console.log(person.firstName, person.lastName); // John, Smith
```

- Accessor properties don't have `value` and `writable` descriptors. They instead have `set` and `get` functions that are called when the property is set and when it's read respectively.

### Configuration

- Besides `value`, object properties have 3 special attributes / flags:
    - `writable` - determines whether or not a value is read-only or changeable
    - `enumerable` - determines whether or not a value can be listed in loops
    - `configurable` - determines whether or not the property can be deleted and its flags can be modified.

- By default, all flags are `true`.
- `Object.getOwnPropertyDescriptor` can be used to get full info about a property.
- `Object.defineProperty` can be used to change the property flags and its deletion; it allows `value` to be changed.

> Changing a property to be non-configurable can't be undone. It can't be reverted using `defineProperty`.

- Multi-flag versions of the above methods (`Object.getOwnPropertyDescriptors` and `Object.defineProperties`) exist, and they can be used to clone objects along with all their property descriptors, symbolic and non-enumerable properties.
    - This can't be done using `for...in` loops.

```js
let cloneObj = Object.defineProperties(
	{},
	Object.getOwnPropertyDescriptors(obj),
);
```

- Methods exists that allow us to do whole object configuration instead of individual property configuration: `Object.preventExtensions(obj)`, `Object.seal(obj)`, `Object.freeze(obj)`.

### Cloning

- `Object.assign()` - shallow; copies both string and symbol properties. Nested objects are copied by reference.
- `structuredClone()` - deep clone, with the exception of methods.

### Computed Properties

- Property names are evaluated or "computed" from a variable or an expression.

```js
let veg = prompt("Which veggie to buy?", "peppers");

let cart = {
	[veg]: 5,
	[`Bell ${veg}`]: 4,
};

console.log(cart.peppers); // 5
console.log(cart["Bell peppers"]); // 4
```

### Destructuring

- Works on any iterable.

```js
let person = {};
[person.firstName, person.lastName] = "Jane Doe".split(" ");

let [a, , c, ...remaining] = "abcde";
a; // -> "a"
c; // -> "c"
remaining; // -> ["d", "e"]
```

- Can be used to swap values.

```js
[a, c] = [c, a];
a; // -> "c"
c; // -> "a"
```

- Absent values are `undefined`; Default values, expressions or function calls can replace missing values.

```js
let [x, y] = [];
x; // -> undefined
y; // -> undefined

let [x = 0, y = 0] = [10];
x; // -> 10
y; // -> 0
```

- Similar syntax applies for object destructuring:

```js
let { firstName, lastName } = {
	firstName: "Jane",
	lastName: "Doe",
};
```

- [Read more 📄](https://javascript.info/destructuring-assignment)

### The Global Object

- The global object provides variables and functions that are built into the language or environment and are available anywhere.
    - Browsers: `window`
    - Node.js: `global`
    - Recent cross-environment standardized name for the global object: `globalThis`
- Functions and variables declared in the global-scope with `var` (not `let` or `const`) become properties of the global object.
- Support for modern browser features can be checked by checking their availability as a global object property. Necessary polyfills can the be added.

```js
if (!window.Promise) {
	// Promise polyfill
}
```

## OOP

- Constructor functions are a way to define the an object's template; it contains the set of methods and the properties it can have.

- By convention, they start with a capital letter and name the object type they create; they don't have a return statement.

```js
function EV(make) {
	this.make = make;
	this.describe = function () {
		console.log(`The ${this.make} is an electric vehicle brand.`);
	};
}

const rivian = new EV("Rivian");

console.log(rivian.make);
rivian.describe();
```

- Immediately called constructor functions can be used to create a single complex object:

```js
const rivian = new (function () {
	this.make = "Rivian";
	// ...
})();
```

### Prototype

- Every object in JavaScript has a built-in property - its _prototype_.
- Every function has a prototype that references an object, which contains properties and methods shared by all instances created using that function as a constructor.
- And because the prototype is itself an object, it will have its own prototype. This is called a _prototype chain_.

> [!note] Prototypes allow for inheritance in JavaScript.

- When accessing a property on an object, JavaScript looks for it on the object itself. If not found, it looks up the prototype chain.
- `__proto__` is a getter / setter for an object's `[[Prototype]]`; it exist for historical reasons. Modern JS recommends the use of `Object.getPrototypeOf` / `Object.setPrototypeOf` functions instead.
- For a constructor function `F()`, if the `F.prototype` is set to be an object, creating an object using `new F()` sets its `[[Prototype]]` to that value. This is done only at the time of object creation; changing the value of `F.prototype` after object creation doesn't change the prototype of already created objects.

```js
let car = {
	numWheels: 4,
};

function ElectricCar(name) {
	this.name = name;
}

ElectricCar.prototype = car; // overwrites the default prototype

let ev = new ElectricCar("Rivian"); // ev.__proto__ == car
console.log(ev.numWheels); // 4
```

```js
function Person(name) {
	this.name = name;
}

Person.prototype.greet = function () {
	console.log("hello!");
};

const john = new Person("John");
john.greet(); // hello!
```

- `Object.create()` can be used to create an object with a specified prototype object.

```js
const personPrototype = {
	greet() {
		console.log("hello!");
	},
};

const john = Object.create(personPrototype);
john.greet(); // hello!
```

- Function Constructors

```js
const personPrototype = {
	greet() {
		console.log(`Hi, my name is ${this.name}`);
	},
};

function Person(name) {
	this.name = name;
}

Object.assign(Person.prototype, personPrototype);
// same as
// Person.prototype.greet = personPrototype.greet;
```

```js
Storage.prototype.set = function (key, value) {
	this.setItem(key, JSON.stringify(value));
};

Storage.prototype.get = function (key) {
	var value = this.getItem(key);

	return value && JSON.parse(value);
};

localStorage.set("obj", {
	name: "john",
	age: 34,
});

console.log(localStorage.get("obj"));
```

> Only object properties and methods are shared; but an object's state is not.

- `Object.prototype` is the most basic prototype; all objects have it by default. Its prototype is `null`.
    - All properties of `Object.prototype` have an [[enumerable]] value of `false`.
- The prototype can only either be an object or `null`.
- `this` isn't affected by prototypes; in a method, a getter or a setter call, `this` refers to the object before the dot.

---

- Properties that are defined directly in the object, and not on the prototype, are called _own properties_.
    - `Object.keys()` and `Object.values()` only return own properties.
    - `for...in` loops iterate over both own and inherited properties.

```js
// Using the above code
const john = new Person("John");

console.log(Object.hasOwn(john, "name")); // true
console.log(Object.hasOwn(john, "greet")); // false
```

> [!note]
> _Polymorphism_ is when a method has the same name but a different implementation in different classes.

> [!note]
> _Delegation_ is a programming pattern where an object, when asked to perform a task, can perform the task itself or ask another object (its **delegate**) to perform the task on its behalf.

### Classes

> [!important] Classes in JS are syntactic sugar over the existing prototype-based inheritance.

- Class fields and methods are public by default.
    - By convention, protected fields are prefixed with an underscore (`_`). They can be inherited and accessed from a subclass.
    - In modern JS, prepending a property or a method with `#` makes it private.
        - It can only be accessed internally.
        - It can't be accessed using bracket notation.
- Just like literal objects, classes may include getters/setters, computed properties etc.
    - Omitting a setter method makes the property read-only.

```js
class User {
	#name;

	constructor(name) {
		this.#name = name;
	}

	get name() {
		return this.#name;
	}

	set name(value) {
		if (typeof value !== "string") {
			alert("Invalid Data Type");
			return;
		}
		this.#name = value;
	}
}
```

```js
// Class Expression
let User = class {
	sayHi() {
		alert(MyClass); // MyClass name is visible only inside the class
	}
};

// Named Class Expression
let User = class MyClass {
	sayHi() {
		alert(MyClass); // MyClass name is visible only inside the class
	}
};
```

- [[Static Properties & Methods]] can be created in a JS class using the `static` keyword.
    - With the exception of built-in classes, they can be inherited.

```js
class User {
	static staticMethod() {
		alert(this === User);
	}
}

/* ====== OR ====== */
class User {}

User.staticMethod = function () {
	alert(this === User);
};

User.staticMethod(); // true
```

- Taking inheritance into account, the `instanceof` operator allows to check whether an object belongs to a certain class.

```js
john instanceof User;
```

- If a subclass has its own initializations, it must first call the superclass constructor using `super()`, and pass any parameters that the superclass constructor expects.
- When a subclass method replaces the superclass's implementation, it _overrides_ the version in the superclass.

```js
class Person {
	name; // optional; can be initialized to a default value

	// can be omitted
	constructor(name) {
		this.name = name;
	}

	greet() {
		console.log(`Hi, my name is ${this.name}.`);
	}
}
```

```js
class Professor extends Person {
	#teaches;

	constructor(name, teaches) {
		super(name);
		this.#teaches = teaches;
	}

	greet() {
		super.greet();
		this.#introduce();
	}

	#introduce() {
		console.log(`I will teach you ${this.#teaches}.`);
	}
}
```

```js
const john = new Professor("John", "Physics");

john.greet(); // Hi, my name is John, and I will teach you Physics.
john.#teaches; // SyntaxError
```

> [!important]
> Class fields are set on individual objects, not on the `Class.prototype`.

### `this`

- To access its containing object, a method can use the `this` keyword; its value is the object used to call the method. e.g. In `user.greet()`, `this` is `user`.
- `this` is contextual; Its value is evaluated during the run-time, depending on the context.

```js
function fn() {
	console.log(this);
}

let user = {};
user.f = fn;
user.f(); // -> user

fn(); // -> window (non-strict mode)
fn(); // -> undefined (strict mode)
```

- Arrow functions don't have `this`; they inherit the `this` of the nearest non-arrow function ancestor.

#### `this` Contexts

| Execution Context                 | Code                            | Value of this                                              |
| --------------------------------- | ------------------------------- | ---------------------------------------------------------- |
| Global                            | N/A                             | global object (e.g. `window`)                              |
| Function (Method call)            | `myObj.foo();`                  | `myObj`                                                    |
| Function (Baseless function call) | `foo();`                        | global object (e.g. `window`) (`undefined` in strict mode) |
| Function (Using `call`)           | `foo.call(context, myArg);`     | `context`                                                  |
| Function (Using `apply`)          | `foo.apply(context, [myArgs]);` | `context`                                                  |
| Function (Constructor with new)   | `const newFoo = new Foo();`     | the new instance (e.g. `newFoo`)                           |

## Functional Programming

![[Functional Programming]]

## Scoping

> The **_scope_** is the current context of execution in which values and expressions are available or can be referenced.

- If scopes are layered in hierarchy, child scopes have access to parent scopes, but not vice versa.
- Unlike `var`, variables declared with `let` or `const` belong to an additional scope they were created in. They are block-scoped.
- Variables declared inside a code block (`{...}`, `if`, `for`, `while`) are only visible inside that block.

### Closures

- Functions that remember and have access to their outer scope / environment.
- Use cases:
    - Create private variables and methods for encapsulating data.
        - A commonly used pattern in module design to hide implementation details.
    - Implement memoization, caching expensive function results for improved performance.
        - Used in recursive algorithms.
    - Create function factories (functions with customized behavior).
        - Used in [[functional programming]] and for creating specialized function.
- All [[JavaScript]] functions are inherently closures (with the exception of the `new Function`-created ones).
- A function has _memory_ of the environment it was called in.
- [Read more 📄](https://javascript.info/closure)

```js
// Encapsulation
function createCounter() {
	let count = 0;

	return {
		increment: () => ++count,
		getCount: () => count,
	};
}

const counter = createCounter();
counter.increment();
counter.increment();
console.log(counter.getCount()); // Outputs: 2
```

```js
// Memoization
function memoizedFib() {
	const cache = {
		0: 1,
		1: 1,
	};

	return function fib(n) {
		if (n < 2 || n in cache) {
			return cache[n];
		}

		cache[n] = fib(n - 1) + fib(n - 2);
		return cache[n];
	};
}
```

```js
// Function Factories
function multiplyBy(factor) {
	return function (number) {
		return number * factor;
	};
}

const double = multiplyBy(2);
const triple = multiplyBy(3);

console.log(double(5)); // Outputs: 10
console.log(triple(5)); // Outputs: 15
```

## Control Flow

### Conditionals

```js
switch (expression) {
	case case1:
		// code
		break;
	case case2:
		// code
		break;
	case case3:
	case case4:
		// code for grouped case
		break;

	/*...*/

	default:
	// code
}
```

> [!important]
> Equality check with `switch` statements is strict.

### Loops

- `for ... in` iterates over all the [[enumerable]] properties (keys or indices) of an object.
- `for ... of` iterates over the _numeric_ property values of an object.

```js
const arr = ["x", "y", "z"];

for (let i in arr) {
	console.log(i); // '0', '1', '2'
}

for (let i of arr) {
	console.log(i); // 'x', 'y', 'z'
}
```

- `map()` / `filter()` - create new collections with operations performed.
- Standard `for` loop:

```js
for (initializer; condition; finalExpression) {
	// code to run
}
```

- `while` loop

```js
initializer;
while (condition) {
	// code to run

	finalExpression;
}
```

- `do...while` loop - code is always executed at least once.

```js
initializer;
do {
	// code to run

	finalExpression;
} while (condition);
```

- `break` - exit loops or a block of code entirely.
- `continue` - skip to the next iteration; only used with loops.
- `label` - prefix a statement with an identifier to refer to it later with `break` or `continue`.

```js
let i, j;

loop1: for (i = 0; i < 3; i++) {
	// first 'for' statement - "loop1"
	loop2: for (j = 0; j < 3; j++) {
		// second 'for' statement - "loop2"
		if (i === 1 && j === 1) {
			break loop1;
		}
		console.log(`i = ${i}, j = ${j}`);
	}
}
```

### Ternary / conditional operator

- `condition ? 'if' code : 'else' code`
- Directives like `break` and `continue` can't be used with this operator.
- It's recommended to use this operator to return a value depending on a condition.
    - Avoid using it as a replacement for `if...else` statements to run expressions.

```js
// ⛔
age >= 18 ? alert("yes") : alert("no");

// ✅
let accessAllowed = age >= 18 ? "yes" : "no";
```

### Error Handling

- _Syntax errors_ are spelling errors that cause the program to stop running part way thru.
- _Logic errors_ are errors resulting in incorrect or unintended results.
- Errors are commonly handled using `try...catch` statements.
- Errors with asynchronous code can be handled using the `.catch()` method with Promises as well as using a `try...catch` block with `async`/`await`.

```js
openMyFile();
try {
	await writeMyFile(theData); // This may throw an error
} catch (e) {
	handleError(e); // If an error occurred, handle it
} finally {
	closeMyFile(); // Always close the resource
}
```

- Errors can be thrown using `throw`.

```js
throw new Error("This is a custom error");
```

- Custom error types can also be created by extending the `Error` class.

```js
class CustomError extends Error {
	constructor(message) {
		super(message);
		this.name = "CustomError";
	}
}

throw new CustomError("This is a custom error");
```

- Unlike [[Java]], JavaScript doesn't support multiple catch blocks.
    - A common workaround is using conditionals to check the types.

```js
try {
	/* ... */
} catch (error) {
	if (error instanceof TypeError) {
		console.error("Type error:", error.message);
	} else if (error instanceof RangeError) {
		console.error("Range error:", error.message);
	} else {
		console.error("Unknown error:", error.message);
	}
}
```

#### Built-in Types

- `Error` - Generic error
- `SyntaxError` - Syntax error in the code
- `ReferenceError` - Reference to an `undefined` variable
- `TypeError` - Operation on an inappropriate type
- `RangeError` - Number outside of valid range

## Functions

> Functions are of object type.

- **[[Method]]s** - functions that are part of objects.
- Objects arguments are passed by reference.
- Functions created thru a declaration are [[Hoisting|hoisted]]; They can be invoked before they're defined.
    - In strict mode, their scoping is limited to the block they are in.
- Functions create thru an expression are assigned to a variable at run time when their execution is reached; hence, they can't be invoked before their definition.
- Anonymous functions don't have a name; they are often passed as arguments to other functions. e.g. `input.addEventListener("keypress", function(e) {})`.
- Functions can also be created from a string that's passed at run time using the `new Function()` syntax.
    - Can be used to execute code received from a server dynamically.
    - This type of function doesn't remember the environment it's created in; The `[[Environment]]` is set to reference global.
- Named function expressions (NFEs) allow a function to reference / access itself only internally.
    - [Read more 📄](https://javascript.info/function-object#named-function-expression)

```js
// Function declaration / statement
function fn() {
	/* code */
}

// Function expression / literal
const fn = function () {
	/* code */
};

// Named function expression (NFE)
const fn = function func() {
	/* code */
};

fn(); // ✅
func(); // ⛔

// Arrow functions
const fn = () => {
	/* code */
};

// 'new Function' syntax
let fn = new Function([arg1, arg2, ...argN], functionBody);
let add = new Function("a", "b", "return a + b");
```

> Passing in named parameter functions with parenthesis calls the function immediately.

```js
input.addEventListener("keypress", fn()); // ⛔

input.addEventListener("keypress", fn); // ✅
```

- Parameters can have default values:

```js
function fn(a, b = 5) {
	/* code */
}

function fn(a, b = getSum()) {
	/* code */
}
```

- When a function is passed as default parameter,
    - it is evaluated when the calling function is ran.
    - it is evaluated every time if and only if an argument is not provided.

- An empty `return` statement is the same as `return undefined;`.
- No `return` statement in a function returns `undefined`.
- Functions passed to other functions as arguments are called _callback functions_; they used to be the main way async functions were implemented.

### `call`, `apply` & `bind`

- Consider this piece of code:

```js
function greet(phrase) {
	console.log(`${phrase}, ${this.name}!`);
}

let user = {
	name: "John",
	introduce() {
		console.log(`My name is ${this.name}.`);
	},
};
```

#### `call`

- `fn.call(context, ...args)`
- Provides a `this` context a function can execute in.
- It takes an expanded list of arguments.
- It _calls_ the function with the context it provides.

```js
greet.call(user, "Hello"); // Hello, John
```

#### `apply`

- `fn.apply(context, args)`
- Similar to `call`, but it takes an array-like object argument containing the function arguments.

```js
greet.apply(user, ["Hello"]); // Hello, John
```

```js
const nums = [5, 1, 4, 3, 9];

const max = Math.max.apply(null, nums);
```

#### `bind`

- `let boundFn = fn.bind(context, ...args)`
- _Creates_ a new function when invoked takes into account the provided context and the sequence of arguments.
- When an object method is passed as callback (for instance to `setTimeout`), it loses its context (`this`).

```js
setTimeout(user.introduce, 1000); // My name is undefined.
```

- A wrapper function can solve this problem:

```js
setTimeout(() => user.introduce(), 1000); // My name is John.
```

> But if the value of `user` changes before the callback is executed, this will call the wrong object.

- Using `bind` helps with this issue:

```js
let greetUser = greet.bind(user, "Hello");
greetUser("Hello"); // Hello, John

let introduce = user.introduce.bind(user);
setTimeout(introduce, 1000);
```

### Arrow Functions

- don't have their own `this`, `arguments`, `super` or `new.target`
- always anonymous
- can't be invoked with `new`
- shouldn't be used as methods or constructors
- not suitable for `call`, `apply`, and `bind` due to scoping.

- Trying to access `this` will refer to the `this` of the closest non-arrow ancestor function.

```js
let users = {
	name: "Users",
	list: ["John", "Jane", "Alice"],
};

users.displayList = function () {
	this.list.forEach((user) => {
		// this -> users
		console.log(`${this.name}: ${user}`);
	});
};

users.displayList = function () {
	this.list.forEach(function (user) {
		// TypeError: this is undefined
		console.log(`${this.name}: ${user}`);
	});
};
```

### Recursion

- The _execution context_ stores information about the execution process of a running function. It is an internal data structure which contains details of a function execution: current position of the control flow, current variables, the value of `this`, etc; one per each function call.
- When performing nested calls, the current execution context is stored in a stack for retrieval later; this is called the _execution context stack_.
- The maximum number of nested calls (or subcalls) in a recursive function is called the _recursion depth_; this number is limited in [[JavaScript]] by the engine.
    - The recursion depth is also equal to the maximal number of context in the execution context stack.

### Rest Parameters

- JS doesn't throw an error due to excessive arguments in a function call.
- Remaining or excessive parameters can be passed using the _spread operator_ (`...`); it must be at the end of the parameter list and can be accessed inside the function using array syntax.

```js
function sum(a, b, ...nums) {}
```

- In non-arrow functions, [[JavaScript]] also provides a special array-like iterable object, `arguments`, which contains indexed list of all arguments.

> [!info] **The Spread Operator (`...`)**
> It expands an iterable object like a, string, an array or an object into a list of their values.
>
> It can be used to clone arrays and objects.

## Events

- When an event is fired on an element with parents, the browser runs three different phases:
    - **Capturing**
        - If the element's outermost ancestor, `<html>`, has an event (e.g. `click`) handler registered on it, it's ran.
        - It moves 'down' to the next element inside `<html>` and performs the same task until it reaches the direct parent of the element.
    - **Target**
        - If the `target` property has an event handler for the event registered on it, it's ran.
        - If `bubbles` is `true`, the event is propagated to the direct parent, then the next one and so on until the root element is reached.
        - If `bubbles` is `false`, the event isn't propagated to any ancestors.
    - **Bubbling**
        - The exact opposite of _capturing_ occurs; by default, all events are registered in this phase in modern browsers.
        - If the direct parent of the clicked element has an event (e.g. `click`) handler registered on it, it's ran.
        - It moves 'up' to the next immediate ancestor and performs the same task, and so on until it reaches the root element, `<html>`.

- All JavaScript events go through the _capturing_ and _target_ phases.
- The event object has a function, `stopPropagation()`, which stops the event from bubbling up the chain.

## JSON

- JSON is a (double-quoted) text-based data format that resembles JavaScript object literal format; can only contain properties, and no methods.
    - `JSON.stringify()` skips JS-specific object properties like `Symbol` since JSON is language-independent.
- JSON prohibits circular references.
- Like with `toString()`, objects may provide a built-in `toJSON()`; Calling `JSON.stringify()` implicitly calls `toJSON()` if available.
- Both `JSON.parse()` and `JSON.stringify()` support optional transform functions which allows smart reading / writing.

- _Deserialization_ - converting a string to a native object.
- _Serialization_ - converting a native object to a string; it can be transmitted across a network.

## Asynchronous JavaScript

### Callbacks

- Event handlers are a form of asynchronous programming.
- `XMLHttpRequest` was an early form of an asynchronous API that used event handlers to perform async operations.
- Using callbacks-based asynchronous programming leads to code that's harder to read and debug. It leads to the problem known as _callback hell_ or _pyramid of doom_.

### Promises

- To avoid the "callback hell", modern JS uses Promises as the basis for asynchronous programming instead of callbacks.
- A **_promise_** is an object that's returned by an asynchronous function; it represents the current state of an async operation, and it can be any of:
    - _pending_ - promise created and in the process.
    - _fulfilled_ - success; `then()` handler is called.
    - _rejected_ - failure; `catch()` handler is called.
- The term _settled_ is used to refer to a non-pending state: either fulfilled or rejected.

> [!note]
> A promise is _resolved_ if it is settled, or if it has been "locked in" to follow the state of another promise.

- For instance, `fetch()` is the promise-based alternative to `XMLHttpRequest` (XHR).
- Promises can be chained because `then()` returns a promise itself.
- Promise objects also provide a `catch()` method for error handling upon rejection / failure.
- To run promises that are independent of one another, we can use:
    - `Promise.all([...promises])` - fulfilled only if _all_ the promises in the array are fulfilled; rejected otherwise.
    - `Promise.any([...promises])` - fulfilled as soon as _any_ one of the promises in the array is fulfilled; rejected if all of them are rejected.

> [!important]
> The `Promise` constructor returns a promise object. The promise object will become _resolved_ when either of the functions `resolveFn` or `rejectFn` are invoked.
>
> ```ts
> new Promise((resolveFn, rejectFn) => {
> 	/* ... */
> });
> ```

### Async/Await

- Inserting `async` keyword before a function definition makes it asynchronous.
- Inside the `async` function, `await` can then be used before a function call that returns a promise. The code waits at this point until the promise is settled, returning a fulfilled / rejected value.

> [!note]
> Async functions always return a promise.

```js
async function fetchTodos() {
	try {
		const response = await fetch(
			"https://jsonplaceholder.typicode.com/todos",
		);
		if (!response.ok) {
			throw new Error(`HTTP error: ${response.status}`);
		}
		const data = await response.json();
		return data;
	} catch (error) {
		console.error(`Unable to get todos: ${error}`);
	}
}

const promise = fetchTodos();

console.log(promise[0].name); // ⛔
promise.then((data) => console.log(data[0].name)); // ✅
```

> [!example] Example: Implementing a promise-based sleep() function

```js
async function sleep(duration) {
	return new Promise((resolve) => {
		if (duration < 0) throw new Error("Negative Timer");

		setTimeout(resolve, duration);
	});
}

(async () => {
	console.log("Hi!");
	await sleep(5000);
	console.log("Bye!");
})();
// 0s: Hi!
// 5s: Bye!

console.log("Hi!");
sleep(5000).then(() => {
	console.log("Bye!");
});
```

> [!example] Example: Implementing a promise-based alarm() API

```js
function alarm(person, delay) {
	return new Promise((resolve, reject) => {
		if (delay < 0) {
			throw new Error("Alarm delay must be set to a positive value");
		}

		setTimeout(() => {
			resolve(`Wake up, ${person}!`);
		}, delay);
	});
}

alarm("Dave", 2000)
	.then((message) => (output.textContent = message))
	.catch((error) => (output.textContent = `Unable to set alarm: ${error}`));

// using async/await
try {
	const message = await alarm("Dave", 2000);
	output.textContent = message;
} catch (error) {
	output.textContent = `Unable to set alarm: ${error}`;
}
```

> [!example] Example: Implementing the fetch API using Promises & `XMLHttpRequest`

```js
const fetchData = (url) => {
	return new Promise((resolve, reject) => {
		const xhr = new XMLHttpRequest();
		xhr.onreadystatechange = () => {
			if (xhr.readyState === XMLHttpRequest.DONE) {
				const status = xhr.status;
				if (status === 0 || (status >= 200 && status < 400)) {
					resolve(xhr.responseText);
				} else {
					reject("Error!");
				}
			}
		};
		xhr.open("GET", url);
		xhr.send();
	});
};

fetchData("https://jsonplaceholder.typicode.com/todos")
	.then((res) => {
		return JSON.parse(res);
	})
	.then((data) => {
		console.log(data);
	})
	.catch((err) => console.log(err));
```

### Workers

- enable us to run tasks in a separate thread; there are 3 different types:
    - _dedicated / web workers_ - a simple way to run scripts in the background.
    - _shared workers_ - can be accessed from several browsing contexts, e.g. scripts running in different windows or iframes.
    - _service workers_ - act as proxy servers; sit between web applications, the browser, and the network.

## Web APIs

**DOM (Document Object Model)**

![[DOM]]

- The [[DOM|Document Object Model]] represents the document currently loaded in a browser tab.

**AJAX (Asynchronous JavaScript and XML)**

- A general term for sending data asynchronously.
- A technique used by data-driven websites that uses [[HTTP]] requests and [[DOM]] manipulation APIs to update certain parts of a page; This way pages are updated faster (no refresh) and bandwidth is reduced (less data download).
- In the earlier days XHR was used to implement AJAX:

```js
// using try...catch
const request = new XMLHttpRequest();

try {
	request.open("GET", "https://jsonplaceholder.typicode.com/todos");

	request.responseType = "json";

	request.addEventListener("load", () => console.log(request.response));
	request.addEventListener("error", () => console.error("XHR error"));

	request.send();
} catch (error) {
	console.error(`XHR error: ${request.status}`);
}
```

- The "modern" way of making [[HTTP]] requests: `fetch()`
    - `fetch()` is an asynchronous API which returns a Promise.

**Canvas API**

- Enables drawing graphics thru JS and the `<canvas>` element.

```html
<canvas width="480" height="320">
	<p><!-- fallback content --></p>
</canvas>
```

```js
const canvas = document.querySelector("canvas");
const width = canvas.width;
const height = canvas.height;

const ctx = canvas.getContext("2d");
ctx.fillStyle = "rgb(0, 0, 0)";
ctx.fillRect(0, 0, width, height);

function random(number) {
	return Math.floor(Math.random() * (number + 1));
}

function randomRect(x, y) {
	ctx.fillStyle = `rgb(${random(255)}, ${random(255)}, ${random(255)})`;
	ctx.fillRect(x, y, random(width) / 2, random(height) / 2);
}

canvas.addEventListener("click", (e) => {
	randomRect(e.clientX, e.clientY);
});
```

![canvas-demo.png](/assets/images/js.canvas-demo.png)

**Date Object**

- `new Date()`
- A _timestamp_ is an integer number that represents the number of milliseconds passed since `Jan 1st, 1970`; negative values represent dates before `Jan 1st, 1970`.
- If a value exceeds its allowed range, the extra value is distributed automatically.
    - e.g. `new Date(2022, 13, 34)` -> `Mar 06, 2023`
- `Date` to number conversion yields the timestamp same as `date.getTime()`.
- `Date.parse(str)` - reads a date from a string.

**Client-side Storage**

- Cookies
- Web Storage
    - `localStorage`
    - `sessionStorage`
- IndexedDB API
- Cache API
- The browser contains this and many more powerful web APIs like: Audio API, History, IndexedDB, WebGL.

**Third-Party APIs**

- **_REST APIs_**
    - Standard database functions are performed by making [[HTTP]] requests to specific URLs, that include data like search terms encoded in the URL as parameters; these actions can be _CRUD_ operations: creating, reading, updating, or deleting records within a resource.

## Best Practices

- Removing event listeners can improve efficiency for complex applications.
- It’s considered a good practice to minimize the use of global variables. But, they can be useful to store project-level data.
- Use comments to describe how and why the code works.
- Use [JSDoc](https://jsdoc.app/index.html) syntax to document a function's usage, parameters, and its returned value.

```js
/**
 * Returns x raised to the n-th power.
 *
 * @param {number} x The number to raise.
 * @param {number} n The power, must be a natural number.
 * @return {number} x raised to the n-th power.
 */
function pow(x, n) {
	return x ** n;
}
```

- It is considered inefficient and a bad practice to pollute your HTML with JavaScript.

```html
<button onclick="clicked()">Click me!</button>
```

### Async Operations

- Use `promise.all` to execute multiple but _independent_ async operations in parallel, rather than sequentially.
    - This potentially reduces the total time compared to making requests sequentially.

```js
async function fetchData() {
	try {
		const [userData, productData, orderData] = await Promise.all([
			fetch("https://api.example.com/user"),
			fetch("https://api.example.com/products"),
			fetch("https://api.example.com/orders"),
		]);

		const user = await userData.json();
		const products = await productData.json();
		const orders = await orderData.json();

		return { user, products, orders };
	} catch (error) {
		console.error("Error fetching data:", error);
	}
}
```

#### Common Mistakes

- Not Handling Errors Properly
    - Omitting `.catch()` methods for Promises.
    - Neglecting `try/catch` blocks around `await` calls.
- Synchronous Loops with Asynchronous Calls
    - Using `await` inside loops, leading to sequential execution instead of concurrent.
- Forgetting to Mark Functions as `async`
    - Using `await` in non-`async` functions, resulting in syntax errors.
- Ignoring Performance Considerations
    - Creating multiple Promises in a loop without control, leading to performance issues.
- Mixing Patterns
    - Combining callbacks, Promises, and `async/await`, which can create confusion and bugs.

### Naming conventions

- Stick to using Latin characters (0-9, a-z, A-Z) and the underscore character.
- Don't use underscores at the start of variables; it may cause confusion with internal [[JavaScript]] constructs.
- Don't use numbers at the start of variables; this is not allowed.
- Variables are case-sensitive.
- Avoid using JavaScript reserved words as variable names.
- Use `const` when you can, and use `let` when you have to.

---

> [!question]- Interview Emphasis Points
>
> > Concepts / sections to focus on when reading
>
> - Event Delegation / Bubbling
> - DOM traversal & manipulation
> - RegEx
> - `this`
> - [[Hoisting]]
> - IIFEs
> - Prototypes: Prototype Chain & Inheritance
> - Scope & Closure
> - Asynchronous Programming
>     - Async/Await
>     - Callbacks
>     - Promises
> - `setTimeout` & `setInterval`
> - Data Structures: Maps & Sets
> - Functional Programming
>     - HOFs
>     - [[Immutable]]
>     - Pure Functions
>     - First-class Functions
>     - Recursion
>     - Currying
> - Design Patterns: Observer Pattern & Module Pattern

---

## Further

### Books 📚

- [Eloquent JavaScript (Marijn Haverbeke)](https://eloquentjavascript.net/)

### Reads 📄

- [Lydia Hallie's JavaScript Visualized Series (DEV)](https://dev.to/lydiahallie/series/3341)

### Resources 🧩

- [New in ECMAScript (Exploring JS)](https://exploringjs.com/impatient-js/ch_new-javascript-features.html) ⭐

- [The Modern JavaScript Tutorial](https://javascript.info/) ⭐

### Videos 🎥

![JavaScript Visualized - Event Loop, Web APIs, (Micro)task Queue ⭐](https://www.youtube.com/watch?v=eiC58R16hb8)

![JavaScript Error Handling: 5 Things You Aren’t Thinking About! (YouTube)](https://www.youtube.com/watch?v=l62mMMU4ZqA)
