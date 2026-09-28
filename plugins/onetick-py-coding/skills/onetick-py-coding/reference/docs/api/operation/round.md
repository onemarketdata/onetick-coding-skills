# otp.Operation.round

#### ``Operation.round(precision=0)``

Rounds input column with specified `precision`.

Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity.

For values that are exactly half-way between two integers (when the fraction part of value is exactly 0.5),
the rounding method used here is *upwards*, which returns the bigger number.
For other rounding methods see ``otp.math.round`` function.

* **Parameters:**
  **precision** (*int*) – Number from -12 to 12.
  Positive precision is precision after the floating point.
  Negative precision is precision before the floating point.
* **Return type:**
  `Operation`

##### Examples

```
>>> t = otp.Tick(A=1234.5678)
>>> t['B'] = t['A'].round()
>>> t['C'] = t['A'].round(2)
>>> t['D'] = t['A'].round(-2)
>>> otp.run(t)
        Time          A       B        C       D
0 2003-12-01  1234.5678  1235.0  1234.57  1200.0
```

##### SEE ALSO
``__round__``
