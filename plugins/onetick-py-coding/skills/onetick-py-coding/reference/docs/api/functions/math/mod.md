# otp.math.mod

### ``mod(value1, value2)``

Computes the remainder from dividing `value1` by `value2`.

* **Parameters:**
  * **value1** (int, float, ``Operation``, ``Column``)
  * **value2** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=100)
>>> data['MOD'] = otp.math.mod(data['A'], 72)
>>> otp.run(data)
        Time    A  MOD
0 2003-12-01  100   28
```
