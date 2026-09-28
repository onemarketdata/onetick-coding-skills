# otp.Operation.decimal.str

### ``str(precision=8)``

Converts decimal to str.

* **Parameters:**
  **precision** (*Operation* *or* *int*) – Number of digits after floating point.
* **Returns:**
  **result** – String representation of decimal value.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=[otp.decimal(1), otp.decimal(2.17), otp.decimal(10.31861), otp.decimal(3.141593)])
>>> data['X'] = data['X'].decimal.str(precision=3)
>>> data = otp.run(data)
>>> data['X']
0    1.000
1    2.170
2    10.319
3    3.142
Name: X, dtype: object
```
