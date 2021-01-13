-   [6.1 Iteration Protocol](#61-iteration-protocol)
    -   [Iteration Everywhere](#iteration-everywhere)
    -   [Iteration: Protocol](#iteration-protocol)
    -   [Supporting Iteration](#supporting-iteration)

6.1 Iteration Protocol
======================

This section looks at the underlying process of iteration.

### Iteration Everywhere

Many different objects support iteration.

``` {.python}
a = 'hello'
for c in a: # Loop over characters in a
    ...

b = { 'name': 'Ajay', 'password':'foo'}
for k in b: # Loop over keys in dictionary
    ...

c = [1,2,3,4]
for i in c: # Loop over items in a list/tuple
    ...

f = open('foo.txt')
for x in f: # Loop over lines in a file
    ...
```

### Iteration: Protocol

Consider the `for`-statement.

``` {.python}
for x in obj:
    # statements
```

What happens under the hood?

``` {.python}
_iter = obj.__iter__()        # Get iterator object
while True:
    try:
        x = _iter.__next__()  # Get next item
    except StopIteration:     # No more items
        break
    # statements ...
```

All the objects that work with the `for-loop` implement this low-level
iteration protocol.

Example: Manual iteration over a list.

``` {.python}
>>> x = [1,2,3]
>>> it = x.__iter__()
>>> it
<listiterator object at 0x590b0>
>>> it.__next__()
1
>>> it.__next__()
2
>>> it.__next__()
3
>>> it.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

### Supporting Iteration

Knowing about iteration is useful if you want to add it to your own
objects. For example, making a custom container.

``` {.python}
class Inventory:
    def __init__(self):
        self.products = []

    def __iter__(self):
        return self.products.__iter__()
    ...

inv = Inventory()
for p in inv:
    ...
```
