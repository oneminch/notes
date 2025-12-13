- Recursion is a technique where a function calls itself.
- It can be ideal for problems that have a repeating structure.
- A recursive function must have a base case and a recursive step that moves closer to the base case in each step.
- Recursion typically takes up more memory than an iterative version, thus it can be less efficient.
    - e.g. The Big-O of a recursive Fibonacci algorithm is $O(2^n)$.

> Anything that can be implemented recursively can also be done iteratively.

- It can be of 2 types: **Tail recursion** & **Non-tail recursion**.

```python
def factorial(n):
    if n == 1:  # base case
        return 1
    else:
        return n * factorial(n-1) # recursive step
```
