# otp.math.rand

### ``rand(min_value, max_value, seed=None)``

Returns a pseudo-random value in the range between `min_value` and `max_value` (both inclusive).
If `seed` is not specified, the function produces different values each time a query is invoked.
If `seed` is specified, for this seed the function produces the same sequence of values
each time a query is invoked.

* **Parameters:**
  * **min_value** (int, ``Operation``, ``Column``)
  * **max_value** (int, ``Operation``, ``Column``)
  * **seed** (int, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['RAND'] = otp.math.rand(1, 1000)
>>> otp.run(data)  
        Time  A  RAND
0 2003-12-01  1   155
```
