# otp.state.tick_set_tick

### ``tick_set_tick()``

Can be used only in per-tick script function
to define a tick set tick local variable.

Tick set ticks can be used with some methods
of tick sets ``onetick.py.state.tick_set``.

* **Return type:**
  `onetick.py.static` value with tick object.

##### Examples

```
>>> def fun(tick):
...    t = otp.tick_set_tick()
...    if tick.state_vars['SET'].find(t, -1):
...        tick['RES'] = '-1'
```

##### SEE ALSO
``onetick.py.state.tick_set``

### *class* \_TickSetTick(name, owner=None)

Bases: ``TickSequenceTick``

#### ``copy(tick_object)``

Makes passed object to point on the same tick (passed object must be of the same type).

#### ``get_datetime_value(field_name, check_schema=True)``

Get value of the datetime `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is datetime.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.
* **Return type:**
  *Operation*

##### Examples

```
>>> def another_query():
...     t = otp.Ticks(X=['a', 'b', 'c'])
...     t['TS'] = t['TIMESTAMP'] + otp.Milli(1)
...     return t
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         tick['TS'] = t.get_datetime_value('TS')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A                      TS
0 2003-12-01  1 2003-12-01 00:00:00.003
```

#### ``get_double_value(field_name, check_schema=True)``

Get value of the double `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is float.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.
* **Return type:**
  *Operation*

##### Examples

```
>>> def another_query():
...     return otp.Ticks(X=[1.1, 2.2, 3.3])
>>> def fun(tick):
...     tick['SUM'] = 0
...     for t in tick.state_vars['LIST']:
...         tick['SUM'] += t.get_double_value('X')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A  SUM
0 2003-12-01  1  6.6
```

#### ``get_long_value(field_name, dtype=int, check_schema=True)``

Get value of the long `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **dtype** (*type*) – Type to set for output operation. Should be set if value is integer’s subclass, e.g. short or byte.
    Default: int
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is int.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.
* **Return type:**
  *Operation*

##### Examples

```
>>> def another_query():
...     return otp.Ticks(X=[1, 2, 3])
>>> def fun(tick):
...     tick['SUM'] = 0
...     for t in tick.state_vars['LIST']:
...         tick['SUM'] += t.get_long_value('X')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A  SUM
0 2003-12-01  1    6
```

#### ``get_string_value(field_name, dtype=str, check_schema=True)``

Get value of the string `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **dtype** (*type*) – Type to set for output operation. Should be set if value is string’s subclass, e.g. otp.varstring.
    Default: str
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is str.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.
* **Return type:**
  *Operation*

##### Examples

```
>>> def another_query():
...     return otp.Ticks(X=['a', 'b', 'c'])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         tick['S'] = t.get_string_value('X')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A  S
0 2003-12-01  1  c
```

#### ``get_timestamp()``

Get timestamp of the tick.

##### Examples

```
>>> def another_query():
...     return otp.Ticks({'A': [1, 2, 3]})
>>> def fun(tick):
...    for t in tick.state_vars['LIST']:
...        tick['TS'] = t.get_timestamp()
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A                      TS
0 2003-12-01  1 2003-12-01 00:00:00.002
```

* **Return type:**
  *Operation*

#### ``get_value(field_name)``

Get value of the `field_name` of the tick.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(X=[1.1, 2.2, 3.3])
>>> def fun(tick):
...     tick['SUM'] = 0
...     for t in tick.state_vars['LIST']:
...         tick['SUM'] += t.get_value('X')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> otp.run(data)
        Time  A  SUM
0 2003-12-01  1  6.6
```

#### ``is_end()``

Checks if current tick is valid. If false tick can not be accessed.

#### ``next()``

Moves to the next tick in the container.

#### ``prev()``

Moves to the previous tick in the container.

#### ``property schema *: `dict`*``

#### ``set_datetime_value(field_name, value, check_schema=True)``

Set `value` of the datetime `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (int, float, str, ``otp.Operation``) – Value to set or operation which return such value.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is the same as the type of `value`.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.

##### Examples

```
>>> def another_query():
...     t = otp.Ticks(X=['a', 'b', 'c'])
...     t['TS'] = t['TIMESTAMP'] + otp.Milli(1)
...     return t
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         t.set_datetime_value('TS', otp.config['default_start_time'])
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  X         TS
0 2003-12-01 00:00:00.000  a 2003-12-01
1 2003-12-01 00:00:00.001  b 2003-12-01
2 2003-12-01 00:00:00.002  c 2003-12-01
```

#### ``set_double_value(field_name, value, check_schema=True)``

Set `value` of the double `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (float, ``otp.Operation``) – Double value to set or operation which return such value.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is the same as the type of `value`.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(A=[1, 2, 3], X=[1.1, 2.2, 3.3])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         t.set_double_value('X', 1.1)
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  A    X
0 2003-12-01 00:00:00.000  1  1.1
1 2003-12-01 00:00:00.001  2  1.1
2 2003-12-01 00:00:00.002  3  1.1
```

#### ``set_long_value(field_name, value, check_schema=True)``

Set `value` of the long `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (int, ``otp.Operation``) – Long value to set or operation which return such value.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is the same as the type of `value`.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(A=[1, 2, 3], X=[1, 2, 3])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         t.set_long_value('X', 1)
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  A  X
0 2003-12-01 00:00:00.000  1  1
1 2003-12-01 00:00:00.001  2  1
2 2003-12-01 00:00:00.002  3  1
```

#### ``set_string_value(field_name, value, check_schema=True)``

Set `value` of the string `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (str, ``otp.Operation``) – String value to set or operation which return such value.
  * **check_schema** (*bool*) – Check that `field_name` exists in tick’s schema and its type is the same as the type of `value`.
    In some cases you may want to disable this behaviour, for example, when tick sequence
    is updated dynamically in different branches of per-tick script. In this case onetick-py
    can’t deduce if `field_name` exists in schema or has the same type.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(A=[1, 2, 3], X=['a', 'b', 'c'])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         t.set_string_value('X', 'a')
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  A  X
0 2003-12-01 00:00:00.000  1  a
1 2003-12-01 00:00:00.001  2  a
2 2003-12-01 00:00:00.002  3  a
```

#### ``set_value(field_name, value)``

Set `value` of the `field_name` of the tick.

* **Parameters:**
  * **field_name** (str, ``otp.Operation``) – String field name or operation which returns field name.
  * **value** (int, ``otp.Operation``) – Datetime value to set or operation which return such value.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(A=[1, 2, 3], X=[1.1, 2.2, 3.3])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         t.set_value('X', 0.0)
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  A    X
0 2003-12-01 00:00:00.000  1  0.0
1 2003-12-01 00:00:00.001  2  0.0
2 2003-12-01 00:00:00.002  3  0.0
```
