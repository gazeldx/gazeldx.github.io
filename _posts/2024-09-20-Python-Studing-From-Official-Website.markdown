---
layout: post
title:  "Study from Python official website"
date:   2024-09-20 11:34:24
categories: Python
---

# Glossaries
* Todo: study `descriptor`. Understanding descriptors is a key to a deep understanding of Python because they are the basis for many features including functions, methods, properties, class methods, static methods, and reference to super classes.

* Metaclass: The class of a class. Class definitions create a class name, a class dictionary, and a list of base classes.

What is `a class dictionary`?

* `Packages` are a way of structuring Python’s module namespace by using “dotted module names”. 

* function parameters:
```python
def f(pos1, pos2, /, pos_or_kwd, *, kwd1, kwd2):
    pass
      # -----------    ----------     ----------
      #   |             |                  |
      #   |        Positional or keyword   |
      #   |                                - Keyword only
      #    -- Positional only

def cheeseshop(kind, *arguments, **keywords):
    matrix = [
        [1, 2, 3, 4],
        [5, 6, 7, 8],
        [9, 10, 11, 12],
    ]
    list(zip(*matrix)) # [(1, 5, 9), (2, 6, 10), (3, 7, 11), (4, 8, 12)]
```

* `list` can be used for `stack` or `queue`, but for `queue`, `pop(0)` is not efficient, so there is a `collections.deque` there for `queue`.
`deque.popleft()` and `deque.appendleft()` are for the purpose. `deque` may mean double-end queue.

* `modules` are files with functions defined in them.
* Without `__init__.py`, modules would not become packages. Packages are for importing purpose and used with `dotted`. like `sys.xxx`.
* A file could be a `module` or a `package`.
* importing a package, the code inside package `__init__()` will only be executed once.

```python
bugs = 'roaches'
count = 13
area = 'living room'
print(f'Debugging {bugs=} {count=} {area=}')
# Debugging bugs='roaches' count=13 area='living room'
```

* The `str.rjust()` method of string objects right-justifies a string in a field of a given width by padding it with spaces on the left.
* `f = open('workfile', 'w', encoding="utf-8")`, `modes` like `w` is `write`, `r` is `read` (default), `a` is `append`. `modes` default is `text` file, append `b` is `binary` file

```python
with open('workfile', encoding="utf-8") as f:
    read_data = f.read()
# This is good practice since f is closed automatically.
# f.closed # test it, it returns True

with open('workfile', 'w', encoding="utf-8") as f:
    f.write('You are awesome.')
```

* `Counter`
```python
from collections import Counter
note1 = 'aabbbbc'
c1 = Counter(note1) # Counter({'b': 4, 'a': 2, 'c': 1})
note2 = 'ab'
c2 = Counter(note2) # Counter({'b': 1, 'a': 1})
c1 - c2 # Counter({'b': 3, 'a': 1, 'c': 1})

h1 = {'b': 3, 'a': 1, 'c': 1}
h2 = {'b': 2, 'c': 1}
Counter(h1) - Counter(h2) # Counter({'b': 1, 'a': 1})
```

* `JSON`
```python
import json

x = [1, 'simple', 'list']
k = json.dumps(x) # '[1, "simple", "list"]'
l = json.loads(k) # return list equals to `x`
```

