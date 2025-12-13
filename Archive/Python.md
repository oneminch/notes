---
alias: Py
---

## Introduction

- Python is a cross-platform programming language.
- Properties of Python:
    - [[Interpreted Language]]
    - [[Dynamically-Typed Language]]
    - Very [[High-Level Language]]
- Python Variables
    - contain only letters, numbers and underscores
    - can't start with a number
    - should be short and descriptive
    - should be lowercase generally with a few exceptions

## Basics

### Data Types

#### Strings

- A **String** is a series of characters enclosed in quotes: single or double
- **Substrings**: `str[start_index : end_index]`
    - Both indices optional, translating to 0 and string length respectively if omitted.
        - `str[:]` - can be used to clone/copy a string.
        - `end_index` is not inclusive.
- String concatenation can be performed using `+`: e.g. `"hello " + ",world!"`
- **Formatted strings**: `f'My name is {name}'`
    - Similar to [[JavaScript]] ES6 string literals.
- Strings can be repeated using the `*` operator - `print("*" * 5)` -> `*****`
- Multi-line strings are enclosed in triple quotes: `''' string '''` or `""" string """`

```python
print("""
    Name: Jane Doe
    Age: 35
""")
```

- `lstrip()`, `rstrip()` & `strip()` methods can be used to strip whitespace from strings.

#### Numbers

- Working with floats can sometimes lead to values with an arbitrary number of decimal places. This happens in all programming languages and it's due to the way computers represent numbers internally.

**Arithmetic Operators**

- `+`, `-`, `*`, `%`
- `/` - true division
- `//` - floor division; mathematical division that rounds down to nearest integer.
- `**` - power or exponentiation

**Operator Precedence:**

- Parenthesis -> Exponentiation -> ( Multiplication | Division ) -> ( Addition | Subtraction )

**Infinity**

- Negative infinity - `float('-inf')` | `-math.inf`
- Positive infinity - `float('inf')` | `math.inf`

#### Boolean

- `True` | `False`
- `bool(x)` - get the truthy / falsy value of `x`

- **Comparison Operators**: `>`, `>=`, `<`, `<=`, `==`, `!=`

#### None

- `None` is an object that represents the absence of a value.
    - Similar to **null** in [[JavaScript]]

### Logical Operators

- `and`, `or`, `not`

### Control Flow

**`if` statements**

```python
if condition_1:
    print("First condition is true!")
elif condition_2:
    print("Second condition is true!")
else:
    print("Neither condition is true!")
```

`while` loop

```python
while condition:
    # Code Block
```

> [!remember]
> A loop that starts with `while True` will fun infinitely unless it's halted with a `break` statement.

**`for` loops**

```python
for n in range(1, 5):
    print(n)
```

- `range(stop)` | `range(start, stop[, step])`
    - It represents an [[Immutable]] sequence of numbers and is commonly used in for loops.
    - `stop` value is not inclusive.
    - If the `step` argument is omitted, it defaults to `1`.
    - If the `start` argument is omitted, it defaults to `0`.
    - For a positive step, the contents of a range `r` are determined by the formula:
        - `r[i] = start + step*i` where `i >= 0` and `r[i] < stop`.
    - For a negative step, the contents of the range are still determined by the formula:
        - `r[i] = start + step*i`, but the constraints are `i >= 0` and `r[i] > stop`.

### Type Casting

- It is sometimes necessary to avoid type errors. It's important to understand some functions take values as a certain data type and to use other data types casting them to the appropriate type is necessary.

```python
num = 24
print("I'm " + num + " years old.")        # TypeError
print("I'm " + str(num) + " years old.")   # I'm 24 years old.
```

- `int(str)`
- `float(str)`
- `str(int)`
- `str(float)`

### Common Tasks

- Get user input - `input()`
- Save input to variable: `age = input("How old are you? ")`
    - Data is saved as a **string**.
- Print concatenated strings to terminal: `print("You are " + age + " years old.")`
- Print type of data: `type(age)`

## Data Structures

### Lists

```python
lst1 = ["a", "b", "c"]
lst2 = list(("a", "b", "c"))
lst3 = list("hello")
```

- are not [[homogeneous]]
- are zero-indexed.
- `list[-1]` - returns last element
- `list[start_index : end_index]` can be used to return subset of a list.
    - `end_index` is not inclusive; both indices are optional, translating to 0 and list length respectively if omitted.
        - `list[:]` - can be used to clone/copy a list.
            - Setting `list_a = list_b` will only make both list names point to the same list object. Any changes made to either list afterwards is reflected on both.
        - `list[:-1]` - returns all but the last element.
        - `list[-n:]` - returns the last n elements.
- List elements can be unpacked or destructured into variables as follows:

    ```python
    coordinates = [1, 2, 3]

    x, y, z = coordinates
    ```

**List syntax**

- `list[i] = val`
- `list.count(item)`
- `list.index(item)`
- `list.append(item)`
- `list.insert(index, item)`
- `list.extend(another_list)`
- `del list[i]` | `del list[i:j]`
- `list[i:j] = otherlist` - replace ith to jth-1 elements with otherlist
- `list.pop()` - returns and removes last element from the list
- `list.pop(i)` - returns and removes i-th element from the list
- `list.remove(x)` - removes the first item from the list whose value is `x`
- `list1 + list2` - combine two list
- `",".join(list)` - returns a string with list elements separated by comma
- `item in list` - check if item exists in list.
- `*list` - expand list items

**Functions**

- `sum(list)`
- `max(list)`
- `min(list)`
- `set(list)` - remove duplicate elements from a list
- `zip(list1, list2)` - returns list of tuples with nth element of both list1 and list2

**Sorting**

- `list.reverse()` - reverses the order of elements in the list in-place
- `list.sort()` - sorts in-place, returns None
- `sorted(list)` - returns sorted copy of list
- A bit more complicated when the list elements are not homogeneous and all values are not in lowercase.
    - The two functions above take the `reverse=True|False` argument.

**List comprehensions**

- allow the creation a new list based on an existing list but with a shorter syntax.
    - `newlist = [expression for item in iterable if condition == True]`

```python
# Create a list of numbers between 0 and 10
first_ten = [num for num in range(0, 10)]

# Create a list of even numbers between 0 and 10
evens = [num for num in range(0, 10) if num % 2 == 0]

# Create a list of the square of even numbers between 0 and 10
even_squares = [num**2 for num in range(0, 10) if num % 2 == 0]
```

- When the name of a list is used as a condition on an if statement, Python checks whether or not the list is empty; returning `True` if there's at least one element and `False` if empty.

### Tuples

```python
tpl1 = ("a", "b", "c")
tpl2 = tuple(("a", "b", "c"))
tpl3 = tuple("hello")
```

- A collection of objects separated by commas.
- [[Immutable]] lists
- Same as lists in most ways including unpacking.
- The only significant difference is [[Immutable|immutability]].
- Although elements of a tuple can't be modified, the variable holding the tuple can be reassigned.

```python
dimensions = (100, 200)

dimensions[0] = 50		# Invalid
dimensions = (50, 200)	# Valid
```

### Sets

```python
set0 = set()
set1 = {"a", "b", "c"}
set2 = set(("a", "b", "c"))
set3 = set("hello")
set4 = set(["a", "b", "c", "b"])

len(set1)      # 3
"a" in set1    # True
```

- Items are unordered, unindexed, and [[immutable]].
- Duplicate items aren't allowed.

### Dictionaries

- sets of key-value pairs.

```python
person_1 = {
    "name": "Jane Smith",
    "age": 32
}

person_2 = {}
```

- Values can be retrieved:
    - using the same bracket notation used with Lists and Tuples.
    - using the `get()` function
- The `in` operator can be used to check if a key exists in a dictionary.
- Setting, modifying and removing a value can also be done using list syntax - `person_1["alive"] = True` (Add & Modify) and `del person_1["alive"]` (Remove).
    - Similar to Objects in [[JavaScript]] in many ways.

> [!NOTE]
>
> Any type of value can be used as a dictionary key as long as it appears only once (duplicates aren't allowed) and it must be of a type that is immutable.

**Looping thru Dictionaries:**

```python
# loop thru keys (default)
for key in dict_1:
    # Code Block

# loop thru keys with keys()
for key in dict_1.keys():
    # Code Block

# loop thru values with values()
for value in dict_1.values():
    # Code Block

# loop thru pairs with items()
for key, value in dict_1.items():
    # Code Block
```

- Wrapping the iterables with functions like `sorted()` and `set()` can yield additional functionalities. In this case, using `sorted(dict_1.keys())` loops thru keys in order, and `set(dict_1.values())` loops thru values without duplicates.

- Dictionaries can be nested inside other dictionaries and lists and vice versa.

- Dictionaries can be unpacked using `**` (similarly to lists using `*`).

```python
def print_coords(x, y, z):
    print(f"x: {x}, y: {y}, z: {z}")

coords = {"x": 75, "y": 50, "z": 25}

print_coords(**coords)
```

## Functions

```python
def greet(fname, lname):
    """Greet a person"""
    print(f"Hello, {fname} {lname}")


greet("John", "Smith")  # Using positional arguments

greet(lname="Smith", fname="John")  # Using keyword arguments
```

- Text enclosed in triple quotes (`"""`) is called a _docstring_, and it describes the purpose of a function. Python looks for these when it's generating documentation for functions in a program.
- _Default values_ for parameters can be set by assigning it to a value during function definition. These values must be listed after all the parameters with no default values; this avoids confusion when using positional arguments.
    - Similar to default arguments in [[JavaScript]].

```python
# NOTE: no space around the '=' sign
def greet(fname, lname="Smith"):
    # Function code
```

> [!note]
> Lists in [[Python]] are ==passed by reference==. To prevent the original list from being modified, `func_call(list_name[:])`

- The value a function returns is called a _return value_.
- Function calls must not precede their definition.

> [!note]
> In Python, functions are _[[First-Class Functions|first-class citizens]]_.

- [[Keyword arguments]] improve code readability:

```python
def calculate_cost(total, shipping, discount):
    cost = discount * (total + shipping)
    print(f"Your cost is ${cost}")


calculate_cost(total=50, shipping=10, discount=0.15)

# Compared to positional arguments
calculate_cost(50, 10, 0.15)
```

- If both [[positional arguments]] and [[keyword arguments]] are used in the same function call, the latter must appear later.

```python
greet("John", lname="Smith")
```

**Passing an arbitrary number of arguments**

```python
# Python creates tuple out of argument_list
# it can then be used that way
def func(*args):
    # code block

# if used with positional arguments, it must
# be placed last in the function definition
def func(arg_one, arg_two, *args):
    # code block

# Python creates empty dict and populate
# it with the passed key-value pairs
def func(arg_one, arg_two, **kwargs):
    # kwargs can be used
    # here as a dictionary
    # kwargs['a_key'] = arg_one
    # kwargs['b_key'] = arg_two
```

### Anonymous Functions / Lambda Expressions

- Anonymous functions are functions with no name.

```python
# Expressions are single line and are the return value of the function
lambda : <expression>
lambda x1, ... , xk: <expression>
```

**Examples**

```python
full_name = lambda first, last: first.strip().title() + " " + last.strip().title()
full_name("Nikola ", "tesLA")  # 'Nikola Tesla'
```

```python
authors = ["Blake Crouch", "J. K. Rowling", "Ernest Cline"]

# Sorts authors list by their last names
authors.sort(key=lambda name: name.split(" ")[-1].lower())

print(authors)    # ['Ernest Cline', 'Blake Crouch', 'J. K. Rowling']
```

## Modules

- A _module_ is a Python file that holds functions that can be imported into a program using an `import` statement.

**Syntax**

```python
import module	# import an entire Python module (module.py)
import module as mdl	# give an alias to imported module
from module import function_a	# import a single function from module
from module import function_a as func_a	# give an alias to imported function

module.function_a()
mdl.function_a()
function_a()
func_a()

# ------------------------------------------------------------- #

# periods can be used to import nested modules
# To import package/module.py
import package.module
from package.module import function_b, function_c

function_b() # or package.module.function_b()
function_c() # or package.module.function_c()

# ------------------------------------------------------------- #

# import all functions in a module; removes necessity
# to use dot notation but creates confusion by not
# stating imported functions and causing name conflicts
from module import *

function_a()
function_b()
function_c()
```

- When the Python interpreter sees an `__init__.py` file in a directory, it will treat that directory as a package.

- A ==_virtual environment_== is a place on a system where packages can be installed and kept isolated from all other Python packages.

## OOP

### Classes

- Unlike functions and variables, class names should be written in _CamelCase_.

- A [[constructor]] can be written using `__init__`:

```python
class Point:
    list = []

    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.z = 0
        self.list.append((self.x, self.y, self.z))

    def update_z(self, value):
        self.z = value

    def increment_y(self, num):
        self.y += num

    def display(self):
        print(f"({self.x}, {self.y}, {self.z})")

# Ways to instantiate objects,
# modify attributes and invoke methods

point_1 = Point(15, 25)

point_2 = Point(15, 25)
point_2.x = 25
point_2.y = 35
point_2.z = 45
point_2.display()

print(Point.list)

point_3 = Point(15, 25)
point_3.update_z(10)
```

- Classes have two kinds of variables:
    - **Instance variables** or **object variables** that are accessible thru dot notation like below are called _attributes_.
    - **Class variables** belong to a class object created by Python and to the objects instantiated from the class.
        - This means they can be access directly from a class object (e.g. `Point.list`) and from an object created with the class (e.g. `point_1.list`).
        - They allow us to inherently share data between different instances of a class without using global variables.

- All attributes needn't be passed in as parameters if they're assigned a default value. e.g. the `z` attribute in the above code.
- Every [[method]] in a class should have the `self` parameter.
- Classes can be imported in a similar manner as functions:
    - e.g. `from car import Car`, `from car import Car, EV`

- In Python, classes are objects. Each class is an instance of class `type`.
- Every class inherits from a parent class `Object`. Magic methods like `__repr__()` are inherited from this parent class and can be ==overridden== to alter the results of a print statement.

```python
class Person:
    def __init__(self, name):
        self.name = name

    def __repr__(self):
        return self.name


print(Person("John"))
# Prints "John" instead of <__main__.Person object at 0xabcdef>
```

- [[Static Properties & Methods|Static methods]] can be defined using `@staticmethod`.

```python
class Car:
    @staticmethod
    def drive():
        print("Vroooom!")


Car.drive()
Car().drive()
```

> [!note]
> The `is` keyword can be used to check if variables point to the same object reference.

### Inheritance

```python
class Car:
    def __init__(self, make, model, year):
        self.make = make
        self.model = model
        self.year = year

    def drive(self):
        print("Driving...")

class Battery:
    def __init__(self, size=100):
        self.size = size

    def describe_size(self):
        print(f"The car has {self.size} kWh battery.")

class EV(Car):
    def __init__(self, make, model, year):
        super().__init__(make, model, year)
        self.range = 300
        self.battery = Battery()

    # overrides parent drive() method
    def drive(self):
        print("Quietly driving...")

    def describe_range(self):
        print(f"On a full charge, the {self.year} {self.make} {self.model} has a {self.range} mi range.")

class Gasoline(Car):
    pass	# To avoid empty class error


rivian = EV("Rivian", "R1T", 2022)
rivian.drive()
rivian.describe_range()
rivian.battery.describe_size()
```

## Files

### Reading a file

#### Entire file

```python
with open('path/to/file.txt', encoding='utf-8') as f:
    contents = f.read()

print(contents)
```

- `open()` - opens a file in a specified mode (second argument).
    - `'r'` - read mode; default mode if second argument is omitted.
    - `'w'` - write mode.
    - `'a'` - append mode.
    - `'r+'` - read and write mode.
- `with` closes an open file once access to it is no longer needed.
- `read()` method of the file object returns an empty string when it reaches the end of the file which is rendered as a blank line; it can be removed using `rstrip()` on the file contents.

> [!note]
> `readlines()` method of the file object stores each line from a file into a list.
>
> `read()` interprets all text in a file as a _string_.

#### Line by line

```python
with open('path/to/file.txt') as f:
    for line in f:
        print(line)
```

- Each line has an invisible newline character at its end. `print()` adds its own newline character each time it's called. So, each print statement renders a blank line after each line which can be removed by applying `rstrip()` on each line.

### Writing to a file

```python
with open('path/to/file.txt', 'w') as f:
    f.write("Hello, World!\n")
    f.write("This text is written from a Python program.\n")
```

> [!note]
> Python can only write _strings_ to a text file. Numerical data have to be converted to string using `str()`.

### Storing data

- The `json` module allows to `dump()` and `load()` simple Python data structures into and from a file.

```python
import json

nums = [1, 2, 3, 4, 5]

filename = 'nums.json'

with open(filename, 'w') as f:
    json.dump(nums, f)

with open(filename) as f:
    nums_copy = json.load(f)

print(nums_copy)
```

## Exceptions

- Exceptions are objects in Python that help manage errors which arise when a program is running.
- They are handled with `try-except` blocks which tells Python to execute some code, but also what to do if an exception is raised.
- Exit codes
    - `0` - Program terminated successfully.
    - `1` - Program crashed

**Exception handling example**:

```python
try:
    age = int(input("Age: "))
    income = 20000
    risk = income / age
except ZeroDivisionError:
    print("Age cannot be zero.")
except ValueError:
    print("Invalid Value")
except FileNotFoundError:
    # fail silently / do nothing
    pass
finally:
    # executed regardless of an exception
    print(risk)
```

- `else` block can be used to execute code when there are no exceptions raised.

- `raise` can be used to manually raise an exception similar to how `throw` operates in [[JavaScript|JS]]

```python
def get_name():
    name = input("Name: ")

    if len(name) < 5:
        raise ValueError("Missing Name.")

    return name


try:
    name = get_name()
except ValueError as e:
    print(e)
else:
    print(name)
```

> [!note]
> Python uses a `pass` or `...` statement as a placeholder or to _do nothing_ in a block.

## Testing

> A _unit test_ verifies that one specific aspect of a function's behavior is correct.
> A _test case_ is a collection of unit tests that together prove a function behaves as it's supposed to.

- The `unittest` Python module provides tools for testing code.

```python
# sum_calc_function.py
def get_sum(a, b):
    """Get the sum of two numbers"""
    return int(a) + int(b)
```

```python
# calculator.py
from sum_calc_function import get_sum

print("Enter 'q' at any time to quit.")
while True:
    num_1 = input("\nEnter first number: ")
    if num_1 == 'q':
        break

    num_2 = input("Enter second number: ")
    if num_2 == 'q':
        break

result = get_sum(num_1, num_2)
print(f"Sum of {num_1} and {num_2}: {result}.")
```

```python
# test_calculator_function.py
import unittest
from sum_calc_function import get_sum

class CalculatorTestCase(unittest.TestCase):
    """Tests for sum_calc_function.py"""
    def test_get_sum(self):
        result = get_sum("2", "3")
        self.assertEqual(result, 5)


if __name__ == '__main__':
    unittest.main()
```

## Python Best Practices

- Comments should be used to describe the "Why" and "How" of a code not the "What".
- Refactoring and compartmentalization of work is an important part of writing cleaner code which is easy to understand, maintain and extend.
- **Functions**
    - Functions should use descriptive names written with lowercase letters and underscores.
    - Every function should provide a comment that concisely describes its purpose in _docstring_ format.
    - Multiple consecutive function definitions should be separated by two blank lines.
    - All `import` statements should be written at the beginning of program.
- **Classes**
    - Class names should be written in CamelCase with no underscores.
    - Instance and module names should be written in lowercase with underscores between
      words.
    - Every class should have a docstring immediately following the class definition that briefly describes what the class does.
    - Each module should also have a docstring describing what the classes in a module can be used for.
    - Blank lines can be used to organize code.
        - Within a class, use one blank line between method definitions.
        - Within a module, use two blank lines between class definitions.
    - If your program imports a standard library module and a module you wrote, the import statement for the standard library module comes first. The import statement for the module you wrote should follow after a blank line.

---

## Further

### Reads 📄

- [Lecture 9 (CS50's Python)](https://cs50.harvard.edu/python/2022/notes/9/)

### Resources 🧩

- [Full Stack Python](https://www.fullstackpython.com/)
