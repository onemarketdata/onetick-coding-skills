# otp.math.pow

### ``pow(base, exponent)``

Compute the `base` to the power of the `exponent`.

* **Parameters:**
  * **base** (int, float, ``Operation``, ``Column``)
  * **exponent** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=2)
>>> data['RES'] = otp.math.pow(data['A'], 10)
>>> otp.run(data)
        Time  A     RES
0 2003-12-01  2  1024.0
```
