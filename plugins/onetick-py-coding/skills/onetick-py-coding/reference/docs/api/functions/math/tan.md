# otp.math.tan

### ``tan(value)``

Returns the value of trigonometric function tan for the given `value` number expressed in radians.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['TAN'] = otp.math.tan(otp.math.pi() / 4)
>>> otp.run(data)
        Time  A  TAN
0 2003-12-01  1  1.0
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.atan()``
