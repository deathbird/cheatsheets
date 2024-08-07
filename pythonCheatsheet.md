# Python Cheatsheet

## Links
* [Pyenv, virtualenv, virtualenvwrapper guide](https://nickolaskraus.io/articles/isolating-python-environments-with-pyenv-virtualenv-and-virtualenvwrapper/)
* [Virtual environments](https://realpython.com/python-virtual-environments-a-primer/)
* [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/index.html)
* [Understanding slice notation](https://stackoverflow.com/questions/509211/understanding-slice-notation)

## IPDB keyboard shortcuts
```
Ctrl-r 	Reverse-search through command history

Ctrl-d 	Delete next character in line
Ctrl-k 	Cut text from cursor to end of line
Ctrl-u 	Cut text from beginning of line to cursor
Ctrl-y 	Yank (i.e. paste) text that was previously cut
Ctrl-t 	Transpose (i.e., switch) previous two characters

Ctrl-l 	Clear terminal screen
Ctrl-c 	Interrupt current Python command
Ctrl-d 	Exit IPython session
```

## virtualenvwrapper
```
# List python versions available to pyenv for local installation
pyenv install -l

# Install locally a python version
pyenv install 3.9.18

# Show all locally install python versions available to pyenv
pyenv versions

# Create virtual environment and activate it
pyenv virtualenv 3.9.18 txdata_env_name
pyenv activate txdata_env_name

pyenv deactivate

# Delete environment
pyenv virtualenv-delete txdata_env_name
```

## PIP
```bash
pip list  # list installed packages

pip install -r requirements.txt  # install from reqs file
pip install .                    # install from setup.py

# Uninstall all installed dependencies (undo pip install)
pip freeze > installed_packages.txt
pip uninstall -r installed_packages.txt -y

# create <project_name>_egg-info/ directory from setup.py
python setup.py egg_info

# use check to find conflicts
pip check

# Use pipdeptree to display dependency tree
pip install piddeptree
pipdeptree --warn fail

# use pip-compile to check for conflicts
pip install pip-tools
echo "-r django_txdata.egg-info/requires.txt" > requirements.in
pip-compile requirements.in

# Check if a PIP package is installed
pip show virtualenv

```
## Various tips
```python
# check for empty list
if not a:
  print('list is empty')

# Check all items of a list:
all(isinstance(x, (int, long)) for x in lst)

# * pytest: How to execute ipdb.set_trace() at will while running pytest tests
# You should run py.test with -s option (turn off capture output). For example:
py.test -s my_test.py
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

## Debugging
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

## Inspect state - find help
globals() — Return a dictionary representing the current global symbol table. This is always the dictionary of the current module (inside a function or method, this is the module where it is defined, not the module from which it is called):
```
import pprint
pprint.pprint(globals())
```

## modules - packages
```python
import os   # load a module.
os.getcwd() # call a function in module.

# Module's function name can be imported directly by the syntax:
#    from module_name import name_1, name_2, … → import several functions.
#    from module_name import * → import all functions (avoid it generally)
from os import getcwd
# current dir
print(getcwd())

# uses alias to shorten name
import a_ridiculously_long_module_name as short_name
# also works for
import module.submodule.subsubmodule as short_name

# Default “module”
# All global (variable/function) names in Python are considered to be in the pseudo-module namespace named __main__.
print(dir())
# ['__annotations__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__']

print(dir("__main__"))

# Module Search Paths
# Python module search paths is stored in the variable sys.path.
import sys
import pprint
# pretty print module search paths
pprint.pprint(sys.path)

# List Loaded Modules
#   Loaded module names is stored in the variable sys.modules.
import sys
pprint.pprint(sys.modules)

# The super-weapon!!! Use the following to force python 2 code
# to use absolute imports resolution ie
# switch import's behaviour to absolute imports
# https://docs.python.org/2.5/whatsnew/pep-328.html
# https://stackoverflow.com/questions/33743880/what-does-from-future-import-absolute-import-actually-do
from __future__ import absolute_import

# Take for example this structure
# SWS/
#   __init.py__
#   foo.py
#   bar.py
#   time.py
#
# in python 2:
import time # will refere to SWS.time module due to the semantics of
# import in ancient python versions (2.x). To fix it add:
from __future__ import absolute_import # will change the semantics of import to that of python3.x
# and will only refer to the top-level time module

# to import a module in our package we use
# Using an explicit relative import
from . import time
# Or using an absolute import:
import SWS.time as time

# also refer to this hack to resolve name collisions
# https://stackoverflow.com/questions/28590568/how-solve-module-name-collision-in-python
```

## Geting help on CLI (or IDLE)

CLI:
```bash
# use pydoc, the python documentation
pydoc <name>
# Search for a keyword in the synopsis lines of all available modules
pydoc -k <keyword>
# Pop up a graphical interface for finding and serving documentation.
pydoc -g
```

```python
# get into the help system and then get help on modules, keywords or topic.
>>>help()

# get help on specific symbol, function, object etc
>>>x = 2
>>>help(x)

import os
help(os)

# To list all modules
help('modules')

# List Module's Function/Variable Names
dir(module_name)  # list all names exported by the module module_name.
# list all names in current scope (but not standard functions).
dir()
# To list standard functions
import __builtin__
dir(__builtin__)

# print all names exported by the module
import re
print(dir(re))

# list the object of a namespace
>>>dir()  # list current globals
>>>dir(int)

# show objects associated with globals and locals
>>>globals()
>>>locals()
```

## Mocking
https://docs.python.org/3/library/unittest.mock.html#module-unittest.mock

## Various
```python
# https://github.com/transifex/django-txdata/pull/1097/files
# python version string compatibility handling
        if not isinstance(jwt_token, six.string_types):
            return jwt_token.decode('utf-8')
        else:  # pragma: no cover
            return jwt_token


# alternate way
import six

if six.PY3:
    unicode_compat = str
else:
    unicode_compat = unicode

# client code using the above (maybe in some other module):
return unicode_compat(some_string_variable)

# log to stdout, if no logger provided
import sys
sys.stdout.write("blah")
```

```bash
# How do I remove all .pyc files (python bytecode) from a project?
find . -name "*.pyc" -exec rm -f {} \;

```

## Recipes

### Date - Time - Timezones
```python
# current datetime
import datetime
print('Current date/time: {}'.format(datetime.datetime.now()))
>>> Current date/time: 2018-06-29 08:15:27.243860

date_time_str = '2018-06-29 08:15:27.243860'
date_time_obj = datetime.datetime.strptime(date_time_str, '%Y-%m-%d %H:%M:%S.%f')
print('Date:', date_time_obj.date())
print('Time:', date_time_obj.time())
print('Date-time:', date_time_obj)
>>> Date: 2018-06-29
>>> Time: 08:15:27.243860
>>> Date-time: 2018-06-29 08:15:27.243860

# Transform a UTC datetime string to timestamp
sdatetime = "2012-07-23T12:34:12Z"
time.mktime(datetime.datetime.strptime("2012-07-23T12:34:12Z", "%Y-%m-%dT%H:%M:%SZ").timetuple())
>>> 1343046852.0

# Timezones
# install pytz library
import pytz
dtime = dt.datetime.now()
print(dtime)
print(dtime.tzinfo)
>>> 2018-06-29 22:16:36.132767
>>> None

dtime = dt.datetime.now(pytz.utc)
print(dtime)
print(dtime.tzinfo)
>>> 2018-06-29 17:08:00.586525+00:00
>>> UTC

# Converting to timezone aware datetime object
date_time_str = '2018-06-29 17:08:00'
date_time_obj = dt.datetime.strptime(date_time_str, '%Y-%m-%d %H:%M:%S')
timezone = pytz.timezone('America/New_York')
timezone_date_time_obj = timezone.localize(date_time_obj)
print(timezone_date_time_obj)
print(timezone_date_time_obj.tzinfo)
>>> 2018-06-29 17:08:00-04:00
>>> America/New_York

# Converting Timezones
timezone_nw = pytz.timezone('America/New_York')
nw_datetime_obj = dt.datetime.now(timezone_nw)
timezone_london = pytz.timezone('Europe/London')
london_datetime_obj = nw_datetime_obj.astimezone(timezone_london)
print('America/New_York:', nw_datetime_obj)
print('Europe/London:', london_datetime_obj)
>>> America/New_York: 2018-06-29 22:21:41.349491-04:00
>>> Europe/London: 2018-06-30 03:21:41.349491+01:00

```
