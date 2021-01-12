# 5.2 Classes and Encapsulation

When writing classes, it is common to try and encapsulate internal details.
This section introduces a few Python programming idioms for this including
private variables and properties.

### Public vs Private.

One of the primary roles of a class is to encapsulate data and internal
implementation details of an object.  However, a class also defines a
*public* interface that the outside world is supposed to use to
manipulate the object.  This distinction between implementation
details and the public interface is important.

### A Problem

In Python, almost everything about classes and objects is *open*.

* You can easily inspect object internals.
* You can change things at will.
* There is no strong notion of access-control (i.e., private class members)

That is an issue when you are trying to isolate details of the *internal implementation*.

### Python Encapsulation

Python relies on programming conventions to indicate the intended use
of something.  These conventions are based on naming.  There is a
general attitude that it is up to the programmer to observe the rules
as opposed to having the language enforce them.

### Private Attributes

Any attribute name with leading `_` is considered to be *private*.

```python
class Person(object):
    def __init__(self, name):
        self._name = 0
```

As mentioned earlier, this is only a programming style. You can still
access and change it.

```python
>>> p = Person('Ajay')
>>> p._name
'Ajay'
>>> p._name = 'Vishal'
>>>
```

As a general rule, any name with a leading `_` is considered internal implementation
whether it's a variable, a function, or a module name.  If you find yourself using such
names directly, you're probably doing something wrong. Look for higher level functionality.

### Simple Attributes

Consider the following class.

```python
class Product:
    def __init__(self, name, quant, price):
        self.name = name
        self.quant = quant
        self.price = price
```

A surprising feature is that you can set the attributes
to any value at all:

```python
>>> p = Product('MINT', 50, 91.1)
>>> p.quant = 100
>>> p.quant = "hundred"
>>> p.quant = [1, 0, 0]
>>>
```

You might look at that and think you want some extra checks.

```python
p.quant = '50'     # Raise a TypeError, this is a string
```

How would you do it?

### Managed Attributes

One approach: introduce accessor methods.

```python
class Product:
    def __init__(self, name, quant, price):
        self.name = name
        self.set_quant(quant)
        self.price = price

    # Function that layers the "get" operation
    def get_quant(self):
        return self._quant

    # Function that layers the "set" operation
    def set_quant(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected an int')
        self._quant = value
```

Too bad that this breaks all of our existing code. `p.quant = 50`
becomes `p.set_quant(50)`

### Properties

There is an alternative approach to the previous pattern.

```python
class Product:
    def __init__(self, name, quant, price):
        self.name = name
        self.quant = quant
        self.price = price

    @property
    def quant(self):
        return self._quant

    @quant.setter
    def quant(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._quant = value
```

Normal attribute access now triggers the getter and setter methods
under `@property` and `@quant.setter`.

```python
>>> p = Product('MINT', 50, 91.1)
>>> p.quant         # Triggers @property
50
>>> p.quant = 75    # Triggers @quant.setter
>>>
```

With this pattern, there are *no changes* needed to the source code.
The new *setter* is also called when there is an assignment within the class,
including inside the `__init__()` method.

```python
class Product:
    def __init__(self, name, quant, price):
        ...
        # This assignment calls the setter below
        self.quant = quant
        ...

    ...
    @quant.setter
    def quant(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._quant = value
```

There is often a confusion between a property and the use of private names.
Although a property internally uses a private name like `_quant`, the rest
of the class (not the property) can continue to use a name like `quant`.

Properties are also useful for computed data attributes.

```python
class Product:
    def __init__(self, name, quant, price):
        self.name = name
        self.quant = quant
        self.price = price

    @property
    def cost(self):
        return self.quant * self.price
    ...
```

This allows you to drop the extra parantheses, hiding the fact that it's actually a method:

```python
>>> p = Product('PASTE', 100, 490.1)
>>> p.quant # Instance variable
100
>>> p.cost   # Computed Value
49010.0
>>>
```

### Uniform access

The last example shows how to put a more uniform interface on an object.
If you don't do this, an object might be confusing to use:

```python
>>> p = Product('PASTE', 100, 490.1)
>>> a = p.cost() # Method
49010.0
>>> b = p.quant # Data attribute
100
>>>
```

Why is the `()` required for the cost, but not for the quant?  A property
can fix this.

### Decorator Syntax

The `@` syntax is known as "decoration".  It specifies a modifier
that's applied to the function definition that immediately follows.

```python
...
@property
def cost(self):
    return self.quant * self.price
```

More details are given in [Section 7](../07_Advanced_Topics/00_Overview).

### `__slots__` Attribute

You can restrict the set of attributes names.

```python
class Product:
    __slots__ = ('name','_quant','price')
    def __init__(self, name, quant, price):
        self.name = name
        ...
```

It will raise an error for other attributes.

```python
>>> p.price = 385.15
>>> p.prices = 410.2
Traceback (most recent call last):
File "<stdin>", line 1, in ?
AttributeError: 'Product' object has no attribute 'prices'
```

Although this prevents errors and restricts usage of objects, it's actually used for performance and
makes Python use memory more efficiently.

### Final Comments on Encapsulation

Don't go overboard with private attributes, properties, slots,
etc. They serve a specific purpose and you may see them when reading
other Python code.  However, they are not necessary for most
day-to-day coding.

