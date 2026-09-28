# otp.math.asin

### ``asin(value)``

Returns the value of inverse trigonometric function arcsin.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['ASIN'] = otp.math.asin(1).round(4)  # should return pi/2 ~ 1.5708
>>> otp.run(data)
        Time  A    ASIN
0 2003-12-01  1  1.5708
```

otp.math.arcsin() is the alias for otp.math.asin():

```
>>> data = otp.Tick(A=1)
>>> data['ASIN'] = otp.math.arcsin(1).round(4)
>>> otp.run(data)
        Time  A    ASIN
0 2003-12-01  1  1.5708
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.sin()``
