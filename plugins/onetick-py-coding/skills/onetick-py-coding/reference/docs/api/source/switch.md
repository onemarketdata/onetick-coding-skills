# otp.Source.switch

#### ``Source.switch(expr, cases, default=False)``

The method splits data using passed expression for several
outputs by passed cases. This method is an alias for
``Source.split()`` method.

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
>>> r1, r2, r3 = data.switch(data['X'], [otp.nan, otp.range(0, 100)], default=True)
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
``Source.split``, ``onetick.py.range``

##### SEE ALSO
``Source.split()``

**SWITCH** OneTick event processor

