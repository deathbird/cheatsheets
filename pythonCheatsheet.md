# Python Cheatsheet

## Geting help on CLI (or IDLE)

```python
# get into the help system and then get help on modules, keywords or topic.
>>>help()

# get help on specific symbol, function etc
>>>x = 2
>>>help(x)

# list the object of a namespace
>>>dir()  # list current globals
>>>dir(int)

# show objects associated with globals and locals
>>>globals()
>>>locals()
```  
## Built-in data types
* Integers (limited by computer memory)
* Floats (based on C's double)
* Complex numbers: 10 + 2j
* Booleans: True, False

Arithmetic operators:
* \+
* \-
* \*
* \/  (division with integers results in float - python3)
* // (integer division)
* ** (exponentiation)
* % (modulus)

```python
# handle complex numbers
>>> y = 10 + 2j
>>> y.real
10.0
>>> y.imag
2.0

# built-in number function
>>> round(3.5)
4

# import mathematic functions module
>>> import math
>>> math.ceil(3.49)
4

```



