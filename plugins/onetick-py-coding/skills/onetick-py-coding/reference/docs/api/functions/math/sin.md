# otp.math.sin

### ``sin(value)``

Returns the value of trigonometric function sin for the given `value` number expressed in radians.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['SIN'] = otp.math.sin(otp.math.pi() / 6)
>>> otp.run(data)
        Time  A  SIN
0 2003-12-01  1  0.5
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.asin()``
