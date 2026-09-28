# otp.math.gcd

### ``gcd(value1, value2)``

Computes the greatest common divisor between `value1` and `value2`.

* **Parameters:**
  * **value1** (int, float, ``Operation``, ``Column``)
  * **value2** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=99)
>>> data['GCD'] = otp.math.gcd(data['A'], 72)
>>> otp.run(data)
        Time   A  GCD
0 2003-12-01  99    9
```
