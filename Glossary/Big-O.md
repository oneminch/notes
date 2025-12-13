- It provides an **asymptotic upper bound** of the growth rate in the worst case, which helps to quantify algorithmic performance as input size grows infinitely.

> [!quote] Wikipedia
> "Big-O notation is a mathematical notation that describes the limiting behavior of a function when the argument tends towards a particular value or infinity. It is used to classify algorithms according to how their run time or space requirements grow as the input size grows." [^1]

- In Big-O, the growth rate is less than or equal to the specified value.
    - e.g. $f(x) <= O(n)$.
- Complexities ordered from smallest to largest, where n is the size of the input.
    - **Constant Time**: $O(1)$
    - **Logarithmic Time**: $O(log(n))$
    - **Linear Time**: $O(n)$
    - **Linearithmic / Log-linear Time**: $O(nlog(n))$
    - **Quadric Time**: $O(n^2)$
    - **Cubic Time**: $O(n^3)$
    - **Exponential Time**: $O(b^n), b > 1$
    - **Factorial Time**: $O(n!)$

> [!quote] Big-O Cheat Sheet
> ![Big-O Cheat Sheet](big-o-cheat-sheet.png)
>
> - **Source**: [Big-O Cheat Sheet](https://www.bigocheatsheet.com)

> [!note]
> The '_slower_' an algorithm slows down as its input size grows, the better or the more scalable it is.

- When calculating Big-O notation:
    1. Always consider what the worst case scenario is.
    2. Remove constants.
    3. Consider the number of inputs.
    4. Keep the dominant term and drop the rest.
    5. Add steps that are sequential.
    6. Add steps that are nested.

## Properties

- `O(n + c) = O(n)`
- `O(cn) = O(n), c > 0`
- For a polynomial function `f(n)`, the Big-O of f(n) is going to be the Big-O of the term with the largest exponent.
    - e.g. $f(n) = 13n^{2} + 2n^{3} + 6$ would have a Big-O Notation of $O(f(n)) \approx O(n^3)$.

## Examples

1. In the function below, we are dealing with two different inputs with possibly different sizes. Because this loops run independently of one another, the Big-O for the function will be $O(n + m)$ where `n` and `m` are input sizes of `list1` and `list2`.

```python
def sequential_loops(list1, list2):
    for x in list1:
        print(x)  # O(n)

    for y in list2:
        print(y)  # O(m)
```

2. Same as above, we are dealing with two different inputs. Because of the nested loops, the Big-O for this function will be $O(n * m)$ where `n` and `m` are input sizes of `list1` and `list2`.

```python
def nested_loops(list1, list2):
    for x in list1:
        for y in list2:
            print(x, y)
```

[^1]: [Big _O_ notation - Wikipedia](https://en.wikipedia.org/wiki/Big_O_notation)
