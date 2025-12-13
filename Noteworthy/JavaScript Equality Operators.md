## `==` (equality operator) vs. `===` (strict equality operator)

- `==` performs type conversions before comparison.
    - It only compares the values and not their data types.

- `===` doesn't perform type conversions before comparison.
    - It compares both the values and their data types.
    - It's preferred because it tends to result in fewer errors.

```js
true == 1; // true
true === 1; // false
```
