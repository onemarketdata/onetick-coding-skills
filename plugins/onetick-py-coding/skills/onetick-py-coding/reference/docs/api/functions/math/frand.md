# otp.math.frand

### ``frand(min_value=0, max_value=1, *, seed=None)``

Returns a pseudo-random value in the range between `min_value` and `max_value`.

* **Parameters:**
  * **min_value** (float, ``Operation``, ``Column``)
  * **max_value** (float, ``Operation``, ``Column``)
  * **seed** (int, ``Operation``, ``Column``) – If not specified, the function produces different values each time a query is invoked.
    If specified, for this seed the function produces the same sequence of values
    each time a query is invoked.
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=otp.math.frand())
>>> otp.run(data)  
        Time  A     FRAND
0 2003-12-01  1  0.667519
```
