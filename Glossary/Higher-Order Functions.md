---
alias: HOF
---

- Higher-Order Functions take one or more functions as arguments and/or return a function as a result. They perform operations on other functions.
- Common [[JavaScript]] array methods such as `filter()`, `reduce()` and `forEach()` are higher-order functions.

```js
const nums = [1, 2, 3, 4, 5, 6];

const oddNums = numbers.filter((number) => number % 2 !== 0);

// oddNums: [1, 3, 5]
```
