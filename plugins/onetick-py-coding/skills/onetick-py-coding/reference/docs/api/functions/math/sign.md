# otp.math.sign

### ``sign(value)``

Compute the sign of the `value`.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['SIGN_POS'] = otp.math.sign(123)
>>> data['SIGN_ZERO'] = otp.math.sign(0)
>>> data['SIGN_NEG'] = otp.math.sign(-123)
>>> otp.run(data)
        Time  A  SIGN_POS  SIGN_ZERO  SIGN_NEG
0 2003-12-01  1         1          0        -1
```
