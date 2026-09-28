# otp.state.tick_set

### ``tick_set(insertion_policy, key_fields, default_value=None, scope='query', schema=None)``

Defines a state tick set.

* **Parameters:**
  * **insertion_policy** ( *'oldest'* *or*  *'latest'*) – ‘oldest’ specifies not to overwrite ticks with the same keys.
    ‘latest’ makes the last inserted tick overwrite the one with the same keys (if existing).
  * **key_fields** (*str* *,* *list* *of* *str*) – The values of the specified fields will be used as keys.
  * **default_value** (``otp.Source``, ``eval query``) – Evaluated query to initialize tick set from.
  * **scope** (*str*) – Scope for the state variable.
    Possible values are: query, branch, cross_symbol, all_inputs, all_outputs
  * **schema** (*dict* *,* *list*) – 

    Desired schema for the created tick set. If not passed, schema will be inherited from `default_value`.
    if `default_value` is not passed as well, schema will contain fields of the main Source object.

    If schema is passed as a list, it will select only these fields from the schema of `default_value`
    or main ``Source`` object.
* **Return type:**
  *TickSet*

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'B', otp.Ticks(B=[1, 1, 2, 2, 3, 3]))
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)[['B']]
   B
0  1
1  2
2  3
```

### ``class TickSet(*args, insertion_policy, key_fields, **kwargs)``

Represents a tick set.
This class should only be created with ``onetick.py.state.tick_set()`` function
and should be added to the ``onetick.py.Source.state_vars()`` dictionary
of the ``onetick.py.Source`` and can be accessed only via this dictionary.

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.state_vars['SET'].dump()
```

##### SEE ALSO
``TickSequenceTick``

#### ``property key_fields``

Get key fields for this tick set

#### ``dump(when_to_dump='every_tick', **kwargs)``

Propagates all ticks from a given tick sequence upon the arrival of input tick.
Timestamps of all propagated ticks are equal to the input tick’s TIMESTAMP.

* **Parameters:**
  * **propagate_input_ticks** (*bool*) – Propagate input ticks or not.
  * **when_to_dump** (*str*) – 
    * first_tick - Propagates once before input ticks. There must be at least one input tick.
    * before_tick - Propagates once before input ticks.
      Content will be propagated even if there are no input ticks.
    * every_tick - Propagates before *each* input tick.
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
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'B', otp.eval(another_query))
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)[['B']]
   B
0  1
1  2
2  3
```

#### ``update(where=1, value_fields=None, erase_condition=0)``

Insert into or delete ticks from tick set.
Can be used only on Source directly.

* **Parameters:**
  * **where** (``Operation``) – Selection of input ticks that will be inserted into tick set.
    By default, all input ticks are selected.
  * **value_fields** (*list* *of* *str*) – List of value fields to be inserted into tick sets.
    If param is empty, all fields of input tick are inserted.
    Note that this applies only to non-key fields (key-fields are always included).
    If new fields are added to tick set, they will have default values according to their type.
    If some fields are in tick set schema but not added in this method, they will have default values.
  * **erase_condition** (``Operation``) – Selection of input ticks that will be erased from tick set.
    If it is set then `where` parameter is not taken into account.
  * **inplace** (*bool*) – If `True` current source will be modified else modified copy will be returned
* **Return type:**
  if `inplace` is False then returns ``Source`` copy.

##### Examples

```
>>> data = otp.Ticks(A=[1, 2, 3], B=[4, 5, 6], C=[7, 8, 9])
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.state_vars['SET'].update(value_fields=['B'])
>>> data = data.state_vars['SET'].update(data['A'] == 2)
>>> data = data.state_vars['SET'].update(erase_condition=data['A'] == 2)
```

```
>>> data = data.first().state_vars['SET'].dump(when_to_dump='first_tick')
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  4
1 2003-12-01  3  6
```

#### ``find(field_name, default_value=None, *key_values, throw=False, **named_keys)``

Finds a tick in the specified tick set for the given keys.
If `field_name` is a string, it returns the value of the specified field from the found tick.
If a tick with the given keys is not found, the default value is returned.
If `field_name` is ``TickSequenceTick``,
the entire tick is returned.

* **Parameters:**
  * **field_name** (str, ``TickSequenceTick``) – The field to return the value from or an object for returning an entire tick.
  * **default_value** – The value to be returned if the key is not found.
    If `default_value` omitted and the key is not found,
    default “zero” value for the field type will be returned.
    If `throw` is True, the positional argument for `default_value`
    counts as the first `key_values` argument.
  * **key_values** – The same number of arguments as the number of keys in the tick set, in the same order as they
    were specified when defining the tick set.
    Values can be specified explicitly or taken from the specified columns. Columns
    must be specified as `source['col']` as opposed to just `'col'`.
    `key_values` are optional: if not specified (and `named_keys` are not specified either),
    the values for the keys are taken from the tick’s columns.
  * **named_keys** – Can be used instead of `key_values` by specifying the `key=value` named arguments.
  * **throw** (*bool*) – Raise an exception if the key is not found.
    If `True`, the positional argument for `default_value` counts as the first `key_values` argument.
* **Return type:**
  *Operation*

##### Examples

Create a tick set keyed by the values of `A`. Look up the value of `B`
in the tick set for the value of `A` in the current tick.

```
>>> data = otp.Tick(A=1, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', 999)
>>> otp.run(data)
        Time  A    B
0 2003-12-01  1    4
```

A key can be specified explicitly (i.e., not taken from the key fields of the current tick).

```
>>> data = otp.Tick(C=777, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', 999, 1)
>>> otp.run(data)
        Time   C    B
0 2003-12-01  777   4
```

A key can be specified explicitly as a `key=value` pair.

```
>>> data = otp.Tick(C=777, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', 999, A=1)
>>> otp.run(data)
        Time   C    B
0 2003-12-01  777   4
```

Columns can be used as keys.

```
>>> data = otp.Tick(C=1, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', 999, data['C'])
>>> otp.run(data)
        Time   C   B
0 2003-12-01   1   4
```

The `default_value` is returned if the key is not found.

```
>>> data = otp.Tick(A=555, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', 999)
>>> otp.run(data)
        Time  A    B
0 2003-12-01 555  999
```

Throw an exception if the key is not found (there is no `default_value` when `throw=True`):

```
>>> data = otp.Tick(A=555, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data['B'] = data.state_vars['SET'].find('B', throw=True)
>>> otp.run(data) 
Traceback (most recent call last):
Exception: 9: ERROR:
```

`find` can be used in `otp.Source.script`.

```
>>> def fun(tick):
...     tick['B'] = tick.state_vars['SET'].find('B', 0, 1)
>>> data = otp.Tick(A=1, B=2)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1, B=4)))
>>> data = data.script(fun)
>>> otp.run(data)
        Time  A    B
0 2003-12-01  1    4
```

In `otp.Source.script` `find` can be used with `TickSetTick`. It allows looking up a whole tick,
rather than the value of a single field:

```
>>> def fun(tick):
...     t = otp.tick_set_tick()
...     if tick.state_vars['SET'].find(t, 2):
...         tick['B'] = t['B']
...         tick['C'] = t['C']
...         tick['D'] = t['D']
>>> def another_query():
...     return otp.Ticks(B=[1, 2, 3], C=[4, 5, 6,], D=[7, 8, 9])
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'B', otp.eval(another_query))
>>> data = data.script(fun)
>>> otp.run(data)
        Time  A  B  C  D
0 2003-12-01  1  2  5  8
```

#### ``find_by_named_keys(field_name, default_value=None, **named_keys)``

Alias for find with restricted set of parameters

##### SEE ALSO
``find``

* **Return type:**
  *Operation*

#### ``find_or_throw(field_name, *key_values)``

Alias for find with restricted set of parameters

##### SEE ALSO
``find``

* **Return type:**
  *Operation*

#### ``erase(*key_values, **named_keys)``

Erase tick(s) from tick set with keys or through
``TickSequenceTick``

* **Parameters:**
  * **key_values** (*list* *,* *optional*) – List of tick set’s keys values that will be used to find tick.
    If single TickSequenceTick is used, only it is removed.
    If two TickSequenceTicks are used, the whole (excluding right boundary) interval between them is removed.
  * **named_keys** (*dict* *,* *optional*) – Dict of tick set’s keys named and values that will be used to find tick.
* **Returns:**
  * ``Operation`` that evaluates to boolean.
  *  *(1 if tick was erased, and 0 if tick was not in tick set).*
* **Return type:**
  *Operation*

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick['B'] = tick.state_vars['SET'].erase(1)
...     tick.state_vars['SET'].erase(A=1)
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'B', otp.eval(otp.Ticks(B=[1, 2, 3])))
>>> data['C1'] = data.state_vars['SET'].erase(1)
>>> data['C2'] = data.state_vars['SET'].erase(1)
```

```
>>> otp.run(data)
        Time  A  C1  C2
0 2003-12-01  1   1   0
```

```
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)
        Time  B
0 2003-12-01  2
1 2003-12-01  3
```

Can be used with ``execute()`` method
to do erasing without returning result as ``Operation``:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'B', otp.eval(otp.Tick(B=2)))
>>> data = data.execute(data.state_vars['SET'].erase(B=2))
```

Example with single TickSetTick:

```
>>> def fun(tick):
...     tick['RES'] = 0
...     for tt in tick.state_vars['set']:
...         if tick.state_vars['set'].erase(tt):
...             tick['RES'] += 1
...     tick['LEN'] = tick.state_vars['set'].get_size()
>>> data = otp.Tick(X=1)
>>> data.state_vars['set'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Ticks(A=[1, 2, 3])))
>>> data = data.script(fun)
>>> otp.run(data)
        Time  X  RES  LEN
0 2003-12-01  1    3    0
```

Example with two TickSetTicks:

```
>>> def fun(tick):
...     t1 = otp.tick_set_tick()
...     t2 = otp.tick_set_tick()
...     if tick.state_vars['set'].find(t1, 2) + tick.state_vars['set'].find(t2, 4) == 2:
...         tick.state_vars['set'].erase(t1, t2)
...     for tt in tick.state_vars['set']:
...         tick['RES'] += tt.get_value('A')
>>> data = otp.Tick(X=1, RES=0)
>>> data.state_vars['set'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Ticks(A=[1, 2, 3, 4])))
>>> data = data.script(fun)
>>> otp.run(data)
        Time  X  RES
0 2003-12-01  1    5
```

#### ``erase_by_named_keys(**named_keys)``

Alias for erase with restricted set of parameters

##### SEE ALSO
``erase``

* **Return type:**
  *Operation* | None

#### ``clear()``

Clear tick set.
Can be used in per-tick script and on Source directly.

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
...     tick.state_vars['SET'].clear()
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1)))
>>> data = data.state_vars['SET'].clear()
```

```
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)
Empty DataFrame
Columns: [A, Time]
Index: []
```

#### ``insert(tick_object=None)``

Insert tick into tick set.

* **Parameters:**
  **tick_object** – Can be set only in per-tick script.
  If not set the current tick is inserted into tick set.
* **Returns:**
  * ``Operation`` that evaluates to boolean.
  *  *(1 if tick was inserted, and 0 if tick was already presented in tick set).*
* **Return type:**
  *Operation* | None

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick.state_vars['SET'].insert(tick)
...     tick.state_vars['SET'].insert()
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data['B'] = data.state_vars['SET'].insert()
>>> data['C'] = data.state_vars['SET'].insert()
```

```
>>> otp.run(data)
        Time  A  B  C
0 2003-12-01  1  1  0
```

```
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)
        Time  A
0 2003-12-01  1
```

Can be used with ``execute()`` method
to do insertion without returning result as ``Operation``:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.execute(data.state_vars['SET'].insert())
```

Inserting current tick in script by passing it to insert method apply the changes made to tick:

```
>>> def fun(tick):
...     tick['A'] = 2
...     tick.state_vars['SET'].insert(tick)
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('latest', 'A')
>>> data = data.script(fun)
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)
        Time  A
0 2003-12-01  2
```

If you want to insert current tick without applied changes use input attribute:

```
>>> def fun(tick):
...     tick['A'] = 2
...     tick.state_vars['SET'].insert(tick.input)
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('latest', 'A')
>>> data = data.script(fun)
>>> data = data.state_vars['SET'].dump()
>>> otp.run(data)
        Time  A
0 2003-12-01  1
```

#### ``get_size()``

Get size of the tick set.
Can be used in per-tick script or in Source operations directly.

* **Return type:**
  ``Operation`` that evaluates to float value.

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick['B'] = tick.state_vars['SET'].get_size()
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data['B'] = data.state_vars['SET'].get_size()
```

```
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  0
```

#### ``size()``

##### SEE ALSO
``get_size``

* **Return type:**
  *Operation*

#### ``present(*key_values)``

Check if tick with these key values is present in tick set.

* **Return type:**
  ``Operation`` that evaluates to boolean (float value 1 or 0).

##### Examples

Can be used in per-tick script:

```
>>> def fun(tick):
...     tick['B'] = tick.state_vars['SET'].present(1)
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A')
>>> data = data.script(fun)
```

Can be used in source columns operations:

```
>>> data = otp.Tick(A=1)
>>> data.state_vars['SET'] = otp.state.tick_set('oldest', 'A', otp.eval(otp.Tick(A=1)))
>>> data['B'] = data.state_vars['SET'].present(1)
>>> data['C'] = data.state_vars['SET'].present(2)
```

```
>>> otp.run(data)
        Time  A  B  C
0 2003-12-01  1  1  0
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
