- In [[JavaScript]], the `enumerable` attribute is one of the several internal attributes (or flags) of an object key / property. It determines whether or not the corresponding property is accessible from a `for...in` loop or `Object.keys()` method.
- Properties created thru object literals and property initialization are `enumerable` by default.

```js
let obj = {
	firstName: "john",
	lastName: "smith",
};

obj.age = 33;
```

- `obj.propertyIsEnumerable(prop)` can be used to determine the enumerability of a property.
