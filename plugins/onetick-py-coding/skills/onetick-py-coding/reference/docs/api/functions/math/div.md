# otp.math.div

### ``div(value1, value2)``

Computes the quotient by dividing `value1` by `value2`.

* **Parameters:**
  * **value1** (int, float, ``Operation``, ``Column``)
  * **value2** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=100)
>>> data['DIV'] = otp.math.div(data['A'], 72)
>>> otp.run(data)
        Time    A  DIV
0 2003-12-01  100    1
```
