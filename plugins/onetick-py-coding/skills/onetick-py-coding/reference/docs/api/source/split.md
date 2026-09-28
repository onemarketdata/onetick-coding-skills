# otp.Source.split

#### ``Source.split(expr, cases, default=False)``

The method splits data using passed expression `expr` for several
outputs by passed `cases`. The method is the alias for the ``Source.switch()``

* **Parameters:**
  * **expr** (*Operation*) – column or column based expression
  * **cases** (*list*) – list of values or ``onetick.py.range`` objects to split by
  * **default** (*bool*) – `True` adds the default output
  * **self** (*Source*)
* **Return type:**
  Outputs according to passed cases, number of outputs is number of cases plus one if `default=True`

##### Examples

```
>>> data = otp.Ticks(X=[0.33, -5.1, otp.nan, 9.4])
>>> r1, r2, r3 = data.split(data['X'], [otp.nan, otp.range(0, 100)], default=True)
>>> otp.run(r1)
                     Time   X
0 2003-12-01 00:00:00.002 NaN
>>> otp.run(r2)
                     Time     X
0 2003-12-01 00:00:00.000  0.33
1 2003-12-01 00:00:00.003  9.40
>>> otp.run(r3)
                     Time    X
0 2003-12-01 00:00:00.001 -5.1
```

##### SEE ALSO
``Source.switch``, ``onetick.py.range``

##### SEE ALSO
``Source.switch()``

**SWITCH** OneTick event processor

