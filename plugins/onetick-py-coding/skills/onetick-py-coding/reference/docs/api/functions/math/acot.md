# otp.math.acot

### ``acot(value)``

Returns the value of inverse trigonometric function arccot.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['ACOT'] = otp.math.acot(1).round(4)  # should return pi/4 ~ 0.7854
>>> otp.run(data)
        Time  A    ACOT
0 2003-12-01  1  0.7854
```

otp.math.arccot() is the alias for otp.math.acot():

```
>>> data = otp.Tick(A=1)
>>> data['ACOT'] = otp.math.arccot(1).round(4)
>>> otp.run(data)
        Time  A    ACOT
0 2003-12-01  1  0.7854
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.cot()``
