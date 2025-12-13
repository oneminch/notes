- **Memoization** is a common ==dynamic programming== technique that ensures a function doesn't run for the same inputs more than once by keeping a cache of the input-result mappings for the given inputs (usually in a hash map type data structure).

**Python: Wrapping the function in a class**

```python
# Fibonacci
class Fib():
    def __init__(self):
        self.cache = {}

    def calc(self, n):
        # Base Case
        if n in [0, 1]:
            return n

        if n in self.cache:
            return self.cache[n]

        result = self.calc(n-1) + self.calc(n-2)
        self.cache[n] = result

        return result


print(Fib().calc(5))
```

**Python: Using a closure / decorator**

```python
# Fibonacci
def fib():
    cache = {}

    def calc(n):
        # Base Case
        if n in [0, 1]:
            return n

        if n in cache:
            return cache[n]

        result = calc(n-1) + calc(n-2)
        cache[n] = result

        return result

    return calc


memoized_fib = fib()
print(memoized_fib(5))
```

- By caching the results of some inputs, the above implementations of the Fibonacci calculator reduces repeated function calls for the same inputs. For the input `6`, the number of repeated calls is reduced by 7, which is significant considering the small input and the memory usage of recursive functions:

> [!example] Function Call Tree Example
> ![Memoized Fibonacci](algo-memoized-fibonacci.svg)
>
> > [!info]- Source
> > [Interview Cake](https://www.interviewcake.com/concept/python/memoization)

**JavaScript: Clousures + IIFEs**

```javascript
// Unique Paths (https://leetcode.com/problems/unique-paths/)
var uniquePaths = (function (m, n) {
	let cache = {};

	const func = function (m, n) {
		if (m === 1 && n === 1) return 1;
		if (m === 0 || n === 0) return 0;

		const key1 = `${m},${n}`;
		const key2 = `${n},${m}`;

		if (key1 in cache || key2 in cache) {
			result = cache[key1];
		} else {
			result = func(m - 1, n) + func(m, n - 1);
			cache[key1] = result;
			cache[key2] = result;
		}
		return result;
	};
	return func;
})();
```
