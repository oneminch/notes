- When returning the value,
    - `return value++` (postfix) returns the original value before the value is increased.
    - `return ++value` (prefix) increases the value and returns the updated value. [^1]

```js
const foo = (x) => x++;
const bar = (x) => ++x;

foo(1); // 1
bar(1); // 2
```

[^1]: [++value vs value++](https://thisthat.dev/postfix-increment-vs-prefix-increment/)
