# otp.math.cos

### ``cos(value)``

Returns the value of trigonometric function cos for the given `value` number expressed in radians.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['COS'] = otp.math.cos(otp.math.pi() / 3)
>>> otp.run(data)
        Time  A  COS
0 2003-12-01  1  0.5
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.acos()``
