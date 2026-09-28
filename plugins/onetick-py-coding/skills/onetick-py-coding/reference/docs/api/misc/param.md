# otp.param

### ``class OnetickParameter(name, dtype, string_literal=True)``

Bases: ``Operation``

Data type representing OneTick parameter.

This object can be used in all places where ``Operation`` can be used
and also in some additional places, e.g. some functions’ parameters.

* **Parameters:**
  * **name** (*str*) – The name of the parameter.
  * **dtype** – The type of the parameter.
  * **string_literal** (*bool*) – 

    By default, OneTick inserts string parameters as is in the graph,
    and they are interpreted as an expression.

    In case this parameter is `True`
    quotes are added when evaluating string parameters, making them the string literals.
    We assume that in most cases it’s desirable for the user’s parameters to be used as a string literal,
    so the default value for this is `True`.

    If you want your string parameters to be interpreted as OneTick expressions, set this to `False`.

##### Examples

Generate tick with parametrized field value:

```
>>> t = otp.Tick(A=otp.param('PARAM', dtype=int))
>>> otp.run(t, query_params={'PARAM': 1})
        Time  A
0 2003-12-01  1
```

Set bucket interval in aggregation as a parameter:

```
>>> t = otp.Ticks(A=[1, 2, 3, 4, 5, 6])
>>> t = t.agg({'A': otp.agg.sum('A')},
...           bucket_units='ticks', bucket_interval=otp.param('PARAM', dtype=int))
>>> otp.run(t, query_params={'PARAM': 3})
                     Time   A
0 2003-12-01 00:00:00.002   6
1 2003-12-01 00:00:00.005  15
```

Parameter `string_literal=False` can be used if you need string to be interpreted as an expression:

```
>>> t = otp.Tick(A=otp.param('PARAM', dtype=str),
...              B=otp.param('PARAM', dtype=str, string_literal=False))
>>> otp.run(t, query_params={'PARAM': 'TOSTRING(NAN())'})
        Time                A    B
0 2003-12-01  TOSTRING(NAN())  nan
```
