# otp.state.var

### ``var(default_value, scope='query')``

Defines a state variable. Supports int, float and string values.

* **Parameters:**
  * **default_value** (*any*) – Default value of the state variable
  * **scope** (*str*) – Scope for the state variable. Possible values are: query, branch, cross_symbol.
    Default: query
* **Return type:**
  a state variable that should be assigned to a \_source

##### Examples

```
>>> data = otp.Ticks(dict(X=[0, 1, 2]))
>>> data.state_vars['SUM'] = otp.state.var(0)
>>> data.state_vars['SUM'] += data['X']
>>> data['SUM'] = data.state_vars['SUM']
>>> otp.run(data)[['X', 'SUM']]
   X  SUM
0  0    0
1  1    1
2  2    3
```

### *class* \_StateColumn(name, dtype, obj_ref, default_value, scope)

#### ``modify_from_query(*args, **kwargs)``

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
