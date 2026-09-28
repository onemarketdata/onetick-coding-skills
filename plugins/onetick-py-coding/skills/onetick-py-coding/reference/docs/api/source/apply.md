# otp.Source.apply

#### ``Source.apply(obj)``

Apply object to data source.

* **Parameters:**
  * **obj** (*onetick.py.query* *,* *Callable* *,* *type* *,* *onetick.query.GraphQuery*) – 
    - onetick.py.query allows to apply external nested query
    - python Callable allows to translate python code to similar OneTick’s CASE expression.
      : There are some limitations to which python operators can be used in this callable.
        See `Python callables parsing guide` article for details.
        In `Remote OTP with Ray` any Callable must be decorated with @otp.remote decorator,
        see `Ray usage examples` for details.
    - type allows to apply default type conversion
    - onetick.query.GraphQuery allows to apply a build onetick.query.Graph
  * **self** (*Source*)
* **Return type:**
  `Column`, `Source`

##### Examples

Apply external query to a tick flow. In this case it assumes that query has
only one input and one output. Check the ``query`` examples if you
want to use a query with multiple inputs or outputs.

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> external_query = otp.query('update.otq')
>>> data = data.apply(external_query)
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  2
1 2003-12-01 00:00:00.001  4
2 2003-12-01 00:00:00.002  6
```

Apply a predicate to a column / operation.
In this case value passed to a predicate is column values.
Result is a column.

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data['Y'] = data['X'].apply(lambda x: x * 2)
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  2
1 2003-12-01 00:00:00.001  2  4
2 2003-12-01 00:00:00.002  3  6
```

Another example of applying more sophisticated operation

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data['Y'] = data['X'].apply(lambda x: 1 if x > 2 else 0)
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  0
1 2003-12-01 00:00:00.001  2  0
2 2003-12-01 00:00:00.002  3  1
```

Example of applying a predicate to a Source. In this case value passed
to a predicate is a whole tick. Result is a column.

```
>>> data = otp.Ticks(X=[1, 2, 3], Y=[.5, -0.4, .2])
>>> data['Z'] = data.apply(lambda tick: 1 if abs(tick['X'] * tick['Y']) > 0.5 else 0)
>>> otp.run(data)
                     Time  X     Y  Z
0 2003-12-01 00:00:00.000  1   0.5  0
1 2003-12-01 00:00:00.001  2  -0.4  1
2 2003-12-01 00:00:00.002  3   0.2  1
```

##### SEE ALSO
``onetick.py.query``
``onetick.py.Source.script()``
`Python callables parsing guide`
