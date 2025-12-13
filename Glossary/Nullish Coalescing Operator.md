- The nullish coalescing operator (`??`) returns the first of two operands that is not `null` or `undefined`; it can be used to provide a default value.

```js
result = a ?? b; // is equivalent to

result = a !== null && a !== undefined ? a : b;
```
