# otp.Operation.apply

#### ``Operation.apply(lambda_f)``

Apply function or type to column

* **Parameters:**
  **lambda_f** (*type* *or* *callable*) – 

  if type - will convert column to requested type

  if callable - will translate python code to similar OneTick’s CASE expression.
  There are some limitations to which python operators can be used in this callable.
  See `Python callables parsing guide` article for details.
  In `Remote OTP with Ray` any Callable must be decorated with @otp.remote decorator,
  see `Ray usage examples` for details.

##### Examples

Converting type of the column, e.g. string column to integer:

```
>>> data = otp.Ticks({'A': ['1', '2', '3']})
>>> data['B'] = data['A'].apply(int) + 10
>>> otp.run(data)
                     Time  A   B
0 2003-12-01 00:00:00.000  1  11
1 2003-12-01 00:00:00.001  2  12
2 2003-12-01 00:00:00.002  3  13
```

More complicated logic:

```
>>> data = otp.Ticks({'A': [-321, 0, 123]})
>>> data['SIGN'] = data['A'].apply(lambda x: 1 if x > 0 else -1 if x < 0 else 0)
>>> otp.run(data)
                     Time    A  SIGN
0 2003-12-01 00:00:00.000 -321    -1
1 2003-12-01 00:00:00.001    0     0
2 2003-12-01 00:00:00.002  123     1
```

##### SEE ALSO
``onetick.py.Source.apply()``
`Python callables parsing guide`
