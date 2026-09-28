

# Python callable parsing

There are currently several methods that implement complex translation of python code
to OneTick’s expressions:

- ``onetick.py.Operation.apply()``
- ``onetick.py.Source.apply()``
- ``onetick.py.Source.script()``.

## Translation

The main feature of these methods is that, unlike most of the other ``onetick.py.Source`` methods,
using callables for them is not resulting in these callables being called in python, but rather
these callables being translated from python code to OneTick expressions.

Therefore, these callables will not be called on each tick and they will not be called even once.

Methods ``onetick.py.Operation.apply()`` and ``onetick.py.Source.apply()`` work the same
and return a OneTick’s `CASE` expression as a new ``onetick.py.Column`` object,
but the first parameter of the callable for ``onetick.py.Operation.apply()`` represents the current column
and for ``onetick.py.Source.apply()`` it represents the whole source object.

```
>>> t = otp.Tick(A=1)
>>> t['A'].apply(lambda x: x + 1 if x >= 0 else x - 1)
Column(CASE((A) >= (0), 1, (A) + (1), (A) - (1)), <class 'int'>)

>>> t.apply(lambda x: x['A'] + 1 if x['A'] >= 0 else x['A'] - 1)
Column(CASE((A) >= (0), 1, (A) + (1), (A) - (1)), <class 'int'>)
```

Using functions for these methods works the same.

```
>>> t = otp.Tick(A=1)
>>> def fun(x):
...     if x >= 0:
...         return x + 1
...     return x - 1
>>> t['A'].apply(fun)
Column(CASE((A) >= (0), 1, (A) + (1), (A) - (1)), <class 'int'>)

>>> def fun(x):
...     if x['A'] >= 0:
...         return x['A'] + 1
...     return x['A'] - 1
>>> t.apply(fun)
Column(CASE((A) >= (0), 1, (A) + (1), (A) - (1)), <class 'int'>)
```

Method ``onetick.py.Source.script()`` works differently and in this case
python’s callable is translated to OneTick’s per-tick script language and
this script is used with `SCRIPT` event processor and method returns new ``onetick.py.Source`` object.
Also the first argument of the callable represents the special input tick object.

```
>>> t = otp.Ticks(A=[-1, 1])
>>> def fun(tick):
...     tick['B'] = 0
...     if tick['A'] >= 0:
...         tick['B'] = 1
>>> t = t.script(fun)
>>> otp.run(t)
                     Time  A  B
0 2003-12-01 00:00:00.000 -1  0
1 2003-12-01 00:00:00.001  1  1
```

## Apply methods

``onetick.py.Operation.apply()`` and ``onetick.py.Source.apply()``

For apply methods the main idea is that the logic of the python callable
should be convertible to OneTick’s `CASE` expression.

Python’s lambda-expressions have the same semantic capabilities as OneTick’s `CASE` expressions,
so there are no limitations for them.

For functions some restrictions should be taken into consideration.

Only these operators are supported:

- `if` statement
- `return` statement

```
>>> t = otp.Ticks(A=[-1, 1])
>>> def fun(x):
...     if x['A'] >= 0:
...         return x['A'] + 1
...     return x['A'] - 1
>>> t['X'] = t.apply(fun)
>>> otp.run(t)
                     Time  A   X
0 2003-12-01 00:00:00.000 -1  -2
1 2003-12-01 00:00:00.001  1   2
```

Also, the python code translation is very flexible and allows
some of the python operators and methods to be translated into simpler ones or
to be executed in python and then be translated to OneTick.

- Simple `for` statement can be replaced with it’s duplicated body:

```
>>> t = otp.Tick(A=1)
>>> def fun(x):
...     for i in [999, 55, 1]:
...         if x['A'] > i:
...             return i
...     return -333
>>> t.apply(fun)
Column(CASE((A) > (999), 1, 999, CASE((A) > (55), 1, 55, CASE((A) > (1), 1, 1, -333))), <class 'int'>)
```

- If the first parameter is propagated to inner python callables,
  the code of these callables will also be translated (they won’t be called):

```
>>> t = otp.Tick(A=1)
>>> def inner_fun(x):
...     if x['A'] >= 10:
...         return 10
...     return 0
>>> t.apply(lambda x: inner_fun(x) if x['A'] > 0 else -10)
Column(CASE((A) > (0), 1, CASE((A) >= (10), 1, 10, 0), -10), <class 'int'>)
```

- All other inner python callables will be called once and their result will be inserted in OneTick expression:

```
>>> t = otp.Tick(A=1)
>>> t.apply(lambda x: x['A'] + sum([1, 2, 3, 4, 5]))
Column((A) + (15), <class 'int'>)
```

## Per-tick script

For ``onetick.py.Source.script()`` method more python operators may also be used in the function:

### Adding new and modifying existing columns

It can be done by modifying first parameter of the function.
This parameter represents input tick.

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     tick['A'] += 1
...     tick['B'] = 'B'
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A B
0 2003-12-01  2 B
```

### Filtering ticks

By default, all input ticks are returned in script.
To filter tick out you can use `return False` statement.
If some `return` statements are specified, then all ticks
are filtered out by default, and the user is expected to control
all cases where ticks should be propagated or not.

```
>>> t = otp.Ticks(A=[1, -1])
>>> def fun(tick):
...     if tick['A'] < 0:
...         return False
...     return True
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A
0 2003-12-01  1
```

You can also return `long` values or expressions in a return statement. However,
in OneTick only value `1` is interpreted as `True`. Other returned integer values will be equal to `False`.

### Propagating ticks

`yield` statement allows to propagate tick more than once.

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     yield
...     yield
...     return False
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A
0 2003-12-01  1
1 2003-12-01  1
```

### Local variables

Simple local variables are redefined on each arrived input tick.
Static local variables are defined once and their values are saved
between the arrival of input ticks.

```
>>> t = otp.Ticks(A=[0, 1])
>>> def fun(tick):
...     a = 1234
...     b = otp.static(0)
...     a = a + 1
...     b = b + 1
...     tick['A'] = a * 2
...     tick['B'] = b
>>> t = t.script(fun)
>>> otp.run(t)
                     Time     A  B
0 2003-12-01 00:00:00.000  2470  1
1 2003-12-01 00:00:00.001  2470  2
```

### Python function calls

Simple python function calls results are also inserted into resulting OneTick code.

For example python built-in function `sum` will be replaced with its returned value:

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     tick['A'] = sum([1, 2, 3, 4, 5])
>>> t = t.script(fun)
>>> otp.run(t)
        Time   A
0 2003-12-01  15
```

Python functions can also be translated to OneTick per-tick script syntax,
but only if they have certain signature and defined parameters and return value types:

```
>>> t = otp.Tick(A=7)

>>> def multiply_a(tick, a: int) -> int:
...     return tick['A'] * a

>>> def fun(tick):
...     tick['B'] = multiply_a(tick, 21)
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A    B
0 2003-12-01  7  147
```

### Python `for` statements

Simple `for` statements can also be translated to per-tick script.
Only iterating over simple sequences (lists, tuples) with simple values (string and numbers) is supported.

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     for i in [1, 2, 3, 4, 5]:
...         tick['A'] += i
>>> t = t.script(fun)
>>> otp.run(t)
        Time   A
0 2003-12-01  16
```

### Looping with `while` statement

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     tick['X'] = 0
...     while tick['X'] < 5:
...         tick['X'] += 1
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A  X
0 2003-12-01  1  5
```

### Python `with` statement

`otp.once` context manager can be used to execute code only once for the first tick, not on each arriving tick:

```
>>> t = otp.Ticks(A=[1, 2], B=[3, 4])
>>> def fun(tick):
...     with otp.once():
...         tick['A'] = tick['B']
>>> t = t.script(fun)
>>> otp.run(t)
                     Time  A  B
0 2003-12-01 00:00:00.000  3  3
1 2003-12-01 00:00:00.001  2  4
```

### Tick sequences

You can iterate over tick sequences inside per-tick script.
These sequences should be created outside of the per-tick script.

- `otp.state.tick_sequence_tick()`
- `otp.state.tick_list()`
- `otp.state.tick_set()`
- `otp.state.tick_set_unordered()`
- `otp.state.tick_deque()`

```
>>> t = otp.Tick(A=1)
>>> t.state_vars['list'] = otp.state.tick_list(otp.eval(otp.Ticks(X=[1, 2, 3, 4, 5])))
>>> def fun(tick):
...     for t in tick.state_vars['list']:
...         tick['A'] += t['X']
>>> t = t.script(fun)
>>> otp.run(t)
        Time   A
0 2003-12-01  16
```

### Tick descriptor fields

It’s possible to iterate over tick descriptor fields in per-tick script,
get their names and types.

```
>>> t = otp.Tick(A=1, B=2)
>>> def fun(tick):
...     tick['NAMES'] = ''
...     for field in otp.tick_descriptor_fields():
...         tick['NAMES'] += field.get_name() + ','
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A  B  NAMES
0 2003-12-01  1  2   A,B,
```

### *class* TickDescriptorFields

Class for declaring tick descriptor fields in per-tick script.
Can only be iterated, doesn’t have methods and parameters.

##### Examples

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         tick['NAME'] = field.get_name()
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A NAME
0 2003-12-01  1    A
```

##### SEE ALSO
``TickDescriptorField``

### ``class TickDescriptorField(name, **\_)``

Tick descriptor field object.
Can be accessed only while iterating over
``otp.tick_descriptor_fields``
in per-tick script.

##### Examples

```
>>> t = otp.Tick(A=2, B='B', C=1.2345)
>>> def fun(tick):
...     tick['NAMES'] = ''
...     tick['TYPES'] = ''
...     tick['SIZES'] = ''
...     for field in otp.tick_descriptor_fields():
...         tick['NAMES'] += field.get_name() + ','
...         tick['TYPES'] += field.get_type() + ','
...         tick['SIZES'] += field.get_size().apply(str) + ','
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A  B       C   NAMES                TYPES    SIZES
0 2003-12-01  2  B  1.2345  A,B,C,  long,string,double,  8,64,8,
```

#### ``get_field_name()``

Get the name of the field.

* **Return type:**
  `onetick.py.Operation`

#### ``get_name()``

Get the name of the field.

* **Return type:**
  `onetick.py.Operation`

#### ``get_size()``

Get the size of the type of the field.

* **Return type:**
  `onetick.py.Operation`

#### ``get_type()``

Get the name of the type of the field.

* **Return type:**
  `onetick.py.Operation`

### Tick objects

Tick objects can be created inside per-tick script.
They can be copied or modified, also some methods are available.

- ``otp.state.tick_list_tick``
- ``otp.state.tick_set_tick``
- ``otp.state.tick_deque_tick``
- ``otp.state.dynamic_tick``

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     t = otp.dynamic_tick()
...     t['A'] = 12345
...     tick.copy_tick(t)
>>> t = t.script(fun)
>>> otp.run(t)
        Time      A
0 2003-12-01  12345
```

Input tick object also has method ``copy_tick()``
that can be used to copy data from input tick to the new tick object.

#### \_EmulateInputObject.copy_tick(tick_object)

Copy fields from `tick_obj` to the output tick.
Will only rewrite fields that are presented in this tick and `tick_object`,
will not remove or add any.
Translated to COPY_TICK() function.

### Get or set ticks’ field via Operations

Ticks’ field can be accessed via Operations with return field’s name.

### ``get_long_value(self, field_name)``

Get value of the long `field_name` of the tick.

* **Parameters:**
  **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
* **Return type:**
  *Operation*

##### Examples

```
>>> def fun(tick):
...     tick['TOTAL_INT'] = 0
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'long':
...             tick['TOTAL_INT'] += tick.get_long_value(field.get_name())
>>> t = otp.Tick(INT_1=3, INT_2=5)
>>> t = t.script(fun)
>>> otp.run(t)
        Time  INT_1  INT_2  TOTAL_INT
0 2003-12-01      3      5          8
```

### ``get_double_value(self, field_name)``

Get value of the double `field_name` of the tick.

* **Parameters:**
  **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
* **Return type:**
  *Operation*

##### Examples

```
>>> def fun(tick):
...     tick['TOTAL_DOUBLE'] = 0.0
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'double':
...             tick['TOTAL_DOUBLE'] += tick.get_double_value(field.get_name())
>>> t = otp.Tick(DOUBLE_1=3.1, DOUBLE_2=5.2)
>>> t = t.script(fun)
>>> otp.run(t)
        Time  DOUBLE_1  DOUBLE_2  TOTAL_DOUBLE
0 2003-12-01      3.1      5.2          8.3
```

### ``get_string_value(self, field_name)``

Get value of the string `field_name` of the tick.

* **Parameters:**
  **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
* **Return type:**
  *Operation*

##### Examples

```
>>> def fun(tick):
...     tick['TOTAL_STR'] = ""
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'string':
...             tick['TOTAL_STR'] += tick.get_string_value(field.get_name())
>>> t = otp.Tick(STR_1="1", STR_2="2")
>>> t = t.script(fun)
>>> otp.run(t)
        Time  STR_1  STR_2  TOTAL_STR
0 2003-12-01      1      2          12
```

### ``get_datetime_value(self, field_name)``

Get value of the datetime `field_name` of the tick.

* **Parameters:**
  **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
* **Return type:**
  *Operation*

##### Examples

```
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'nsectime':
...             tick['SOME_DATETIME'] = tick.get_datetime_value(field.get_name())
>>> t = otp.Tick(DATETIME=otp.datetime(2021, 1, 1))
>>> t = t.script(fun)
>>> otp.run(t)
        Time   DATETIME SOME_DATETIME
0 2003-12-01 2021-01-01    2021-01-01
```

### ``set_long_value(self, field_name, value)``

Set `value` of the long `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (int, ``otp.Operation``) – Long value to set or operation which return such value.
* **Return type:**
  `str`

##### Examples

```
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'long':
...             tick.set_long_value(field.get_name(), 5)
>>> t = otp.Tick(INT_1=3)
>>> t = t.script(fun)
>>> otp.run(t)
        Time  INT_1
0 2003-12-01      5
```

### ``set_double_value(self, field_name, value)``

Set `value` of the double `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (float, ``otp.Operation``) – Double value to set or operation which return such value.
* **Return type:**
  `str`

##### Examples

```
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'double':
...             tick.set_double_value(field.get_name(), 5.0)
>>> t = otp.Tick(DOUBLE_1=3.0)
>>> t = t.script(fun)
>>> otp.run(t)
        Time  DOUBLE_1
0 2003-12-01      5.0
```

### ``set_string_value(self, field_name, value)``

Set `value` of the string `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (str, ``otp.Operation``) – String value to set or operation which return such value.
* **Return type:**
  `str`

##### Examples

```
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'string':
...             tick.set_string_value(field.get_name(), '5')
>>> t = otp.Tick(STR_1='3')
>>> t = t.script(fun)
>>> otp.run(t)
        Time  STR_1
0 2003-12-01      5
```

### ``set_datetime_value(self, field_name, value)``

Set `value` of the datetime `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (int, ``otp.Operation``) – Datetime value to set or operation which return such value.
* **Return type:**
  `str`

##### Examples

```
>>> def fun(tick):
...     for field in otp.tick_descriptor_fields():
...         if field.get_type() == 'nsectime':
...             tick.set_datetime_value(field.get_name(), otp.datetime(2021, 1, 1) - otp.Day(1))
>>> t = otp.Tick(DATETIME_1=otp.datetime(2021, 1, 1))
>>> t = t.script(fun)
>>> otp.run(t)
        Time  DATETIME_1
0 2003-12-01      2020-12-31
```

### Using input ticks

In some cases you might want to get tick the way it was before being updated in script.
You can do it with using input attribute.

```
>>> t = otp.Tick(A=1)
>>> def fun(tick):
...     tick['A'] = 2
...     tick['B'] = tick.input['A']
>>> t = t.script(fun)
>>> otp.run(t)
        Time  A  B
0 2003-12-01  2  1
```

### Logging

### ``logf(message, severity, *args)``

Call built-in OneTick `LOGF` function from per-tick script.

* **Parameters:**
  * **message** (*str*) – Log message/format string. The underlying formatting engine is the Boost Format Library:
    `https://www.boost.org/doc/libs/1_53_0/libs/format/doc/format.html`
  * **severity** (*str*) – Severity of message. Supported values: `ERROR`, `WARNING` and `INFO`.
  * **args** (*list*) – Parameters for format string (optional).
* **Return type:**
  `str`

##### Examples

```
>>> t = otp.Ticks({'X': [1, 2, 3]})
```

```
>>> def test_script(tick):
...     otp.logf("Tick with value X=%1% processed", "INFO", tick["X"])
```

```
>>> t = t.script(test_script)
```

##### SEE ALSO
`Per-Tick Script Guide`

### Error handling

### ``throw_exception(message)``

Call built-in OneTick `THROW_EXCEPTION` function from per-tick script.

* **Parameters:**
  **message** (*str*) – Message string that defines the error message to be thrown.
* **Return type:**
  `str`

##### Examples

```
>>> t = otp.Ticks({'X': [1, -2, 6]})
```

```
>>> def test_script(tick):
...     if tick["X"] <= 0:
...         otp.throw_exception("Tick column X should be greater than zero.")
```

```
>>> t = t.script(test_script)
```

##### SEE ALSO
`Per-Tick Script Guide`
