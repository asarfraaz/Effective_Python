-   [6.2 Customizing Iteration](#62-customizing-iteration)
    -   [A problem](#a-problem)
    -   [Generators](#generators)

6.2 Customizing Iteration
=========================

This section looks at how you can customize iteration using a generator
function.

### A problem

Suppose you wanted to create your own custom iteration pattern.

For example, a countdown.

``` {.python}
>>> for x in countdown(10):
...   print(x, end=' ')
...
10 9 8 7 6 5 4 3 2 1
>>>
```

There is an easy way to do this.

### Generators

A generator is a function that defines iteration.

``` {.python}
def countdown(n):
    while n > 0:
        yield n
        n -= 1
```

For example:

``` {.python}
>>> for x in countdown(10):
...   print(x, end=' ')
...
10 9 8 7 6 5 4 3 2 1
>>>
```

A generator is any function that uses the `yield` statement.

The behavior of generators is different than a normal function. Calling
a generator function creates a generator object. It does not immediately
execute the function.

``` {.python}
def countdown(n):
    # Added a print statement
    print('Counting down from', n)
    while n > 0:
        yield n
        n -= 1
```

``` {.python}
>>> x = countdown(10)
# There is NO PRINT STATEMENT
>>> x
# x is a generator object
<generator object at 0x58490>
>>>
```

The function only executes on `__next__()` call.

``` {.python}
>>> x = countdown(10)
>>> x
<generator object at 0x58490>
>>> x.__next__()
Counting down from 10
10
>>>
```

`yield` produces a value, but suspends the function execution. The
function resumes on next call to `__next__()`.

``` {.python}
>>> x.__next__()
9
>>> x.__next__()
8
```

When the generator finally returns, the iteration raises an error.

``` {.python}
>>> x.__next__()
1
>>> x.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

*Observation: A generator function implements the same low-level
protocol that the for statements uses on lists, tuples, dicts, files,
etc.*
