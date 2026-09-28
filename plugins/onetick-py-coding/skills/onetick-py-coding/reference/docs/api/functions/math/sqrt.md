# otp.math.sqrt

### ``sqrt(value)``

Compute the square root of the `value`.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['SQRT'] = otp.math.sqrt(4)
>>> otp.run(data)
        Time  A  SQRT
0 2003-12-01  1   2.0
```
