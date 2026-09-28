# otp.math.cot

### ``cot(value)``

Returns the value of trigonometric function cot for the given `value` number expressed in radians.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['COT'] = otp.math.cot(otp.math.pi() / 4)
>>> otp.run(data)
        Time  A  COT
0 2003-12-01  1  1.0
```

##### SEE ALSO
``onetick.py.math.pi()``, ``onetick.py.math.acot()``
