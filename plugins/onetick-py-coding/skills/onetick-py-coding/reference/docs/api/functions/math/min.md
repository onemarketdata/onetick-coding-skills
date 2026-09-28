# otp.math.min

### ``min(*objs)``

Returns minimum value from list of `objs`.
The objects must be of the same type.

* **Parameters:**
  **objs** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

##### Examples

```
>>> data = otp.Tick(A=1)
>>> data['MIN'] = otp.math.min(-5, data['A'])
>>> otp.run(data)
        Time  A  MIN
0 2003-12-01  1   -5
```
