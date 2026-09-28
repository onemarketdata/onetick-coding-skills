# otp.state.tick_list

### ``tick_list(default_value=None, scope='query', schema=None)``

Defines a state tick list.

* **Parameters:**
  * **default_value** (``otp.Source``, ``eval query``) – Evaluated query to initialize tick list from.
  * **scope** (*str*) – Scope for the state variable.
    Possible values are: query, branch, cross_symbol, all_inputs, all_outputs
  * **schema** (*dict* *,* *list*) – 

    Desired schema for the created tick list. If not passed, schema will be inherited from `default_value`.
    if `default_value` is not passed as well, schema will contain fields of the main Source object.

    If schema is passed as a list, it will select only these fields from the schema of `default_value`
    or main ``Source`` object.
* **Return type:**
  *TickList*

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.Ticks(B=[1, 2, 3]))
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)[['B']]
   B
0  1
1  2
2  3
```

### ``class TickList(name, obj_ref, default_value, scope, schema=None, **kwargs)``

Represents a tick list.
This class should only be created with ``onetick.py.state.tick_list()`` function
and should be added to the ``onetick.py.Source.state_vars()`` dictionary
of the ``onetick.py.Source`` and can be accessed only via this dictionary.

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data = data.state_vars['LIST'].dump()
```

##### SEE ALSO
``TickSequenceTick``

#### ``clear()``

Clear tick list.
Can be used in per-tick script or on Source directly.

inplace: bool
: If `True` current source will be modified else modified copy will be returned.
  Makes sense only if used not in per-tick script.

* **Returns:**
  * if `inplace` is False and method is not used in per-tick script
  * then returns ``Source`` copy.

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick.state_vars['LIST'].clear()
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(otp.Tick(A=1)))
>>> data = data.state_vars['LIST'].clear()
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
Empty DataFrame
Columns: [A, Time]
Index: []
```

#### ``push_back(tick_object)``

Add tick_object to the tick list.
Can only be used in per-tick script.

##### Examples

```
>>> def fun(tick):
...     tick.state_vars['LIST'].push_back(tick)
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data = data.script(fun)
```

```
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
        Time  A
0 2003-12-01  1
```

#### ``get_size()``

Get size of the tick list.
Can be used in per-tick script or in Source operations directly.

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick['B'] = tick.state_vars['LIST'].get_size()
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data['B'] = data.state_vars['LIST'].get_size()
```

```
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  0
```

* **Return type:**
  *Operation*

#### ``size()``

##### SEE ALSO
``get_size``

* **Return type:**
  *Operation*

#### ``sort(field_name, field_type=None)``

Sort the tick list in the ascending order over the specified field.
Integer, float and datetime fields are supported for sorting.
Implementation is per tick script which does a merge sort algorithm.
Implemented algorithm is stable: it should not change the order of ticks with the same field value.
Can only be used Source operations directly.

* **Parameters:**
  * **field_name** (*str*) – Name of the field over which to sort ticks
  * **field_type** (*int* *,* *float* *,* *otp.msectime* *,* *otp.nsectime* *or* *None* *(**default: None* *)*) – Type of the field_name field. If None, type will be taken from the tick list schema.

##### Examples

```
>>> def fun(tick):
...     tick.state_vars['LIST'].push_back(tick)
>>> data = otp.Ticks([
...    ['offset', 'VALUE'],
...    [0, 2],
...    [0, 3],
...    [0, 1],
... ])
>>> data.state_vars['LIST'] = otp.state.tick_list()
>>> data = data.script(fun)
>>> data = data.agg(dict(NUM_TICKS=otp.agg.count()), bucket_time='end')
>>> data = data.state_vars['LIST'].sort('VALUE', int)
>>> data = data.state_vars['LIST'].dump()
```

```
>>> otp.run(data)
        Time  VALUE
0 2003-12-01  1
1 2003-12-01  2
2 2003-12-01  3
```

#### ``erase(tick_object)``

Remove tick_object from the tick list.
Can only be used in per-tick script.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(X=[1, 2])
>>> def fun(tick):
...     for t in tick.state_vars['LIST']:
...         if t['X'] == 1:
...             tick.state_vars['LIST'].erase(t)
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.script(fun)
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.001  2
```

#### ``dump(propagate_input_ticks=False, when_to_dump='first_tick', delimiter=None, added_field_name_suffix=None)``

Propagates all ticks from a given tick sequence upon the arrival of input tick.

Preserves original timestamps from tick list/deque, if they fall into query start/end time range,
and for ticks before start time and after end time, timestamps are changed
to start time and end time correspondingly.

* **Parameters:**
  * **propagate_input_ticks** (*bool*) – Propagate input ticks or not.
  * **when_to_dump** (*str*) – 
    * first_tick - Propagates once before input ticks. There must be at least one input tick.
    * before_tick - Propagates once before input ticks.
      Content will be propagated even if there are no input ticks.
  * **delimiter** ( *'tick'* *,*  *'flag* *or* *None*) – 

    This parameter specifies the policy for adding the delimiter field.
    The name of the additional field is “DELIMITER” + `added_field_name_suffix`.
    Possible options are:
    * None - No additional field is added to propagated ticks.
    * ’tick’ - An extra tick is created after the last tick.
      Also, an additional column is added to output ticks.
      The extra tick has values of all fields set to the defaults (0,NaN,””),
      except the delimiter field, which is set to string “D”.
      All other ticks have this field’s value set to empty string.
    * ’flag’ - The delimiter field is appended to each output tick.
      The field’s value is empty for all ticks except the last tick of the tick sequence, which is string “D”.
  * **added_field_name_suffix** (*str* *or* *None*) – The suffix to add to the name of the additional field.
  * **inplace** (*bool*) – If `True` current source will be modified else modified copy will be returned
* **Return type:**
  if `inplace` is False then returns ``Source`` copy.

##### Examples

```
>>> def another_query():
...     return otp.Ticks(B=[1, 2, 3])
>>> data = otp.Tick(A=1)
>>> data.state_vars['LIST'] = otp.state.tick_list(otp.eval(another_query))
>>> data = data.state_vars['LIST'].dump()
>>> otp.run(data)[['B']]
   B
0  1
1  2
2  3
```

#### ``modify_from_query(query, symbol=None, start=None, end=None, params=None, action='replace', where=None, output_field_name=None)``

Modifies a ``state variable``
by assigning it a value that was resulted from a `query` evaluation.

* **Parameters:**
  * **query** (*callable* *,* *Source*) – 

    Callable `query` should return `Source`. This object will be evaluated by OneTick (not python)
    for every tick. Note python code will be executed only once, so all python’s conditional expressions
    will be evaluated only once too.

    If `query` is a `Source` object then it will be propagated as a query to OneTick.

    If state `var` is a primitive (not tick sequence), then `query` must return only one tick,
    otherwise exception will be raised.
  * **symbol** (*str* *,* *Operation* *,* *dict* *,* *Source* *, or* *tuple* ***Union* *[*[*str* *,* *Operation* *]* *,* *Union* **[*dict* *,* *Source* *]* *]*) – 

    Symbol name to use in `query`. In addition, symbol params can be passed along with symbol name.

    Symbol name can be passed as a string or as an `Operation`.

    Symbol parameters can be passed as a dictionary. Also, the main `Source` object,
    or the object containing a symbol parameter list, can be used as a list of symbol parameters.

    `symbol` will be interpreted as a symbol name or as symbol parameters, depending on its type.
    You can pass both as a tuple.

    If symbol name is not passed, then symbol name from the main source is used.
  * **start** (``otp.datetime``, ``otp.Operation``) – Start time to run the `query`.
    By default the start time of the main query is used.
  * **end** (``otp.datetime``, ``otp.Operation``) – End time to run the `query`.
    By default the end time of the main query is used.
  * **params** (*dict*) – Mapping of the parameters’ names and their values for the `query`.
    ``Columns`` can be used as a value.
  * **action** (*str*) – Specifies whether all ticks should be erased before the query results are inserted into the tick set.
    Possible values are `update` and `replace`.
    For non-tick-sets, you can set the `action` only to `replace`; otherwise, an error is thrown.
  * **where** (*Operation*) – Condition to filter ticks for which the result of the `query` will be joined.
  * **output_field_name** (*str*) – Specifies the output field name for state variables of primitive types,
    in case if the query result contains multiple fields.
* **Returns:**
  Source with joined ticks from `query`
* **Return type:**
  `Source`

##### Examples

Update simple state variable from query:

```python
data = otp.Ticks(A=[1, 2, 3])
data.state_vars['VAR'] = 0
data.state_vars['VAR'] = 7
def fun():
    return otp.Tick(X=123, Y=234)
data = data.state_vars['VAR'].modify_from_query(fun,
                                                output_field_name='X',
                                                where=(data['A'] % 2 == 1))
data['X'] = data.state_vars['VAR']
df = otp.run(data)
print(df)
```

```
                     Time  A    X
0 2003-12-01 00:00:00.000  1  123
1 2003-12-01 00:00:00.001  2  7
2 2003-12-01 00:00:00.002  3  123
```

Update tick sequence from query:

```python
data = otp.Tick(A=1)
data.state_vars['VAR'] = otp.state.tick_list()
def fun():
    return otp.Ticks(X=[123, 234])
data = data.state_vars['VAR'].modify_from_query(fun)
data = data.state_vars['VAR'].dump()
df = otp.run(data)
print(df)
```

```
                     Time    X
0 2003-12-01 00:00:00.000  123
1 2003-12-01 00:00:00.001  234
```

Passing parameters to the `query`:

```python
data = otp.Tick(A=1)
data.state_vars['VAR'] = otp.state.tick_list()
def fun(min_value):
    t = otp.Ticks(X=[123, 234])
    t = t.where(t['X'] > min_value)
    return t
data = data.state_vars['VAR'].modify_from_query(fun, params={'min_value': 200})
data = data.state_vars['VAR'].dump()
df = otp.run(data)
print(df)
```

```
                     Time    X
0 2003-12-01 00:00:00.001  234
```

##### SEE ALSO
**MODIFY_STATE_VAR_FROM_QUERY** OneTick event processor

#### ``property schema *: `dict`*``

Get schema of the tick sequence.
