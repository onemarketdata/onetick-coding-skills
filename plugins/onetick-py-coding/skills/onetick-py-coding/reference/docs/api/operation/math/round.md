# onetick.py.Operation._\_round_\_

#### Operation.\_\_round_\_(precision=0)

Rounds value with specified `precision`.

Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity.

For values that are exactly half-way between two integers (when the fraction part of value is exactly 0.5),
the rounding method used here is *upwards*, which returns the bigger number.
For other rounding methods see ``otp.math.round`` function.

* **Parameters:**
  **precision** (*int*) – Number from -12 to 12.
  Positive precision is precision after the floating point.
  Negative precision is precision before the floating point (and the fraction part is dropped in this case).
* **Return type:**
  `Operation`

##### Examples

By default the `precision` is zero and the number is rounded to the closest integer number
(and to the bigger number when the fraction part of value is exactly 0.5):

```
>>> t = otp.Ticks(A=[-123.45, 123.45, 123.5])
>>> t['ROUND'] = round(t['A'])
>>> otp.run(t)
                     Time       A  ROUND
0 2003-12-01 00:00:00.000 -123.45 -123.0
1 2003-12-01 00:00:00.001  123.45  123.0
2 2003-12-01 00:00:00.002  123.50  124.0
```

Positive precision truncates to the number of digits *after* floating point:

```
>>> t = otp.Ticks(A=[-123.45, 123.45])
>>> t['ROUND1'] = round(t['A'], 1)
>>> t['ROUND2'] = round(t['A'], 2)
>>> otp.run(t)
                     Time       A  ROUND1  ROUND2
0 2003-12-01 00:00:00.000 -123.45  -123.4 -123.45
1 2003-12-01 00:00:00.001  123.45   123.5  123.45
```

Negative precision truncates to the number of digits *before* floating point
(and the fraction part is dropped in this case):

```
>>> t = otp.Ticks(A=[-123.45, 123.45])
>>> t['ROUND_M1'] = round(t['A'], -1)
>>> t['ROUND_M2'] = round(t['A'], -2)
>>> otp.run(t)
                     Time       A  ROUND_M1  ROUND_M2
0 2003-12-01 00:00:00.000 -123.45    -120.0    -100.0
1 2003-12-01 00:00:00.001  123.45     120.0     100.0
```

Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity in all cases:

```
>>> t = otp.Ticks(A=[otp.inf, -otp.inf, otp.nan])
>>> t['ROUND_0'] = round(t['A'])
>>> t['ROUND_P2'] = round(t['A'], 2)
>>> t['ROUND_M2'] = round(t['A'], -2)
>>> otp.run(t)
                     Time    A  ROUND_0  ROUND_P2  ROUND_M2
0 2003-12-01 00:00:00.000  inf      inf       inf       inf
1 2003-12-01 00:00:00.001 -inf     -inf      -inf      -inf
2 2003-12-01 00:00:00.002  NaN      NaN       NaN       NaN
```

##### SEE ALSO
``otp.math.round``
``otp.math.floor``
``otp.math.ceil``
