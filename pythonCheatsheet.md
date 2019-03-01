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

### Numbers
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

# handling booleans
>>> x = False
>>> x
False
>>> not x
True
>>> y = True * 2
>>> y
2
```
### List

Mutable. A list can be indexed from its front or back. You can also refer to a subsegment, or
slice, of a list by using slice notation

```python
>>> x = ["first", "second", "third", "fourth"]
>>> x[0]
'first'
>>> x[2]
'third'
>>> x[-1]
'fourth'
>>> x[-2]
'third'
>>> x[1:-1]
['second', 'third']
>>> x[0:3]
['first', 'second', 'third']
>>> x[-2:-1]
['third']
>>> x[:3]
['first', 'second', 'third']
>>> x[-2:]
['third', 'fourth']
```  
Index from the front using positive indices (starting with 0 as the first element).
Index from the back using negative indices (starting with -1 as the last element).
Obtain a slice using [m:n], where m is the inclusive starting point and n is the
exclusive ending point (see table 3.1). An [:n] slice e starts at its beginning, and an
[m:] slice goes to a list’s end.

You can use this notation to add, remove, and replace elements in a list or to obtain
an element or a new list that’s a slice from it:
```python  
>>> x = [1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> x[1] = "two"
>>> x[8:9] = []
>>> x
[1, 'two', 3, 4, 5, 6, 7, 8]
>>> x[5:7] = [6.0, 6.5, 7.0]
>>> x
[1, 'two', 3, 4, 5, 6.0, 6.5, 7.0, 8]
>>> x[5:]
[6.0, 6.5, 7.0, 8]
```  
The size of the list increases or decreases if the new slice is bigger or smaller than the
slice it’s replacing.
Some built-in functions ( len , max , and min ), some operators ( in , + , and * ), the
del statement, and the list methods ( append , count , extend , index , insert , pop ,
remove , reverse , and sort ) operate on lists:

```python
>>> x = [1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> len(x)
9
>>> [-1, 0] + x
[-1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> x.reverse()
>>> x
[9, 8, 7, 6, 5, 4, 3, 2, 1]
```  
The operators + and * each create a new list, leaving the original unchanged. A
list’s methods are called by using attribute notation on the list itself: x.method
(arguments)

### Tuple
Immutable! The operators ( in , + , and * ) and built-in functions ( len , max and min ) operate on them the same way as they do on lists. Index and slice notation work the same way for obtaining elements or slices but can’t be used to add, remove, or replace elements. Also, there are only two tuple methods: count and index.

```python
()
# A one-element tuple needs a comma.
(1,)
(1, 2, 3, 4, 5, 6, 7, 8, 12)
# A tuple, like a list, can contain a mixture of other types as its elements
(1, "two", 3L, 4.0, ["a", "b"], (5, 6))

# list can be converted to a tuple by using the built-in function tuple :
>>> x = [1, 2, 3, 4]
>>> tuple(x)
(1, 2, 3, 4)

# Conversely, a tuple can be converted to a list by using the built-in function list:
>>> x = (1, 2, 3, 4)
>>> list(x)
[1, 2, 3, 4]
```  

### Debugging
Use pdb or even better ipdb. For async handling (eg celery) use rdb.
```python
import ipdb
ipdb.set_trace()

from celery.contrib import rdb
rdb.set_trace() 

# pdb sample commands
# s (step into)
# n (step over)
# r (return - step out of function)
# c (run to end)
```  
pdb docs: https://docs.python.org/3.6/library/pdb.html#pdbcommand-return  
ipdb docs: https://pypi.org/project/ipdb/  

### Mocking
https://docs.python.org/3/library/unittest.mock.html#module-unittest.mock  
 
