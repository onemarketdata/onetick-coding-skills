# otp.math.acos

### ``acos(value)``

Returns the value of inverse trigonometric function arccos.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['ACOS'] = otp.math.acos(-1).round(4)  # should return pi ~ 3.1416
>>> otp.run(data)
        Time  A    ACOS
0 2003-12-01  1  3.1416
```

otp.math.arccos() is the alias for otp.math.acos():

```
>>> data = otp.Tick(A=1)
>>> data['ACOS'] = otp.math.arccos(-1).round(4)
>>> otp.run(data)
        Time  A    ACOS
0 2003-12-01  1  3.1416
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.cos()``
