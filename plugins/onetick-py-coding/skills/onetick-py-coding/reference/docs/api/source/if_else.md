# otp.Source.if_else

#### ``Source.if_else(condition, if_expr, else_expr)``

Shortcut for ``apply()`` with lambda if-else expression

* **Parameters:**
  * **condition** (``Operation``) – 
    - condition for matching ticks
  * **if_expr** (``Operation``, value) – 
    - value or Operation to set if condition is true
  * **else_expr** (``Operation``, value) – 
    - value or Operation to set if condition is false
  * **self** (*Source*)
* **Return type:**
  `Column`

##### Examples

Basic example of apply if-else to a tick flow:

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data['Y'] = data.if_else(data['X'] > 2, 1, 0)
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  0
1 2003-12-01 00:00:00.001  2  0
2 2003-12-01 00:00:00.002  3  1
```

You can also set column value via ``Operation``:

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data['Y'] = data.if_else(data['X'] > 2, data['X'] * 2, 0)
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  0
1 2003-12-01 00:00:00.001  2  0
2 2003-12-01 00:00:00.002  3  6
```

##### SEE ALSO
``onetick.py.Source.apply()``
