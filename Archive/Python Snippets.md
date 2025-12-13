## Check if a key exists in a dictionary

```python
person = {
    name: "Bobby",
    age: "25"
}

name in person         # True
occupation in person   # False
```

## Create a list of alphabets

```python
import string

str_upeer_list = string.ascii_uppercase[:26]
str_lower_list = string.ascii_lowercase[:26]
```

## Create a virtual environment

```bash
$ python -m venv path/to/myenv

$ venv\Scripts\activate.bat
```

- [The python environment setup cheatsheet](https://bholmes.dev/blog/simple-python-env-setup/)

## Get all occurrences (indices) of an item in a list:

```python
lst = [1, 2, 3, 4, 5, 1, 3]

indices = [x for x in range(len(lst)) if lst[x] == 3]
```

## Initialize list of length `n`:

```python
lst = [None]*n

# [None, None, None, None, None, ...]
```

## Loop thru list:

```python
lst = ["a", "b", "c"]

# range() - only index
for i in range(len(lst)):
    # code block

# enumerate() - item and index
for index, item in enumerate(lst):
    print(index, item)

# 0 a
# 1 b
# 2 c

# enumerate() - item and index with a start index
for index, item in enumerate(lst, start=1):
    print(index, item)

# 1 a
# 2 b
# 3 c
```

## Sort part of a list in-place

```python
a[i:j] = sorted(a[i:j])
```

## Swap items in a list

```python
a[i], a[j] = a[j], a[i]
```

## Ternary operator

```python
min = a if a < b else b
```
