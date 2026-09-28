# otp.math.log10

### ``log10(value)``

Compute the base-10 logarithm of the `value`.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['LOG10'] = otp.math.log10(100)
>>> otp.run(data)
        Time  A  LOG10
0 2003-12-01  1    2.0
```
