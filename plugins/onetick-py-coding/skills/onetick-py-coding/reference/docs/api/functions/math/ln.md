# otp.math.ln

### ``ln(value)``

Compute the natural logarithm of the `value`.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['LN'] = otp.math.ln(2.718282)
>>> otp.run(data)
        Time  A   LN
0 2003-12-01  1  1.0
```

##### SEE ALSO
``onetick.py.math.exp()``
