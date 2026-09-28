# otp.math.round

### ``round(value, precision=0, rounding_method='upward')``

Rounds value with specified `precision` and `rounding_method`.

Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity.

* **Parameters:**
  * **precision** (*int*) – Number from -12 to 12.
    Positive precision is precision after the floating point.
    Negative precision is precision before the floating point (and the fraction part is dropped in this case).
  * **rounding_method** (*str*) – Used for values that are exactly half-way between two integers (when the fraction part of value is exactly 0.5).
    Available values are **upward**, **downward**, **towards_zero**, **away_from_zero**.
    Default is **upward**.

##### Examples

Different rounding methods produce different results for values that are exactly half-way between two integers:

```
>>> t = otp.Ticks(A=[-123.45, 123.45, -123.4, 123.6])
>>> t['UPWARD'] = otp.math.round(t['A'], precision=1, rounding_method='upward')
>>> t['DOWNWARD'] = otp.math.round(t['A'], precision=1, rounding_method='downward')
>>> t['TOWARDS_ZERO'] = otp.math.round(t['A'], precision=1, rounding_method='towards_zero')
>>> t['AWAY_FROM_ZERO'] = otp.math.round(t['A'], precision=1, rounding_method='away_from_zero')
>>> otp.run(t).head(2)
                     Time       A  UPWARD  DOWNWARD  TOWARDS_ZERO  AWAY_FROM_ZERO
0 2003-12-01 00:00:00.000 -123.45  -123.4    -123.5        -123.4          -123.5
1 2003-12-01 00:00:00.001  123.45   123.5     123.4         123.4           123.5
```

Note that for other cases all methods produce the same results:

```
>>> otp.run(t).tail(2)
                     Time      A  UPWARD  DOWNWARD  TOWARDS_ZERO  AWAY_FROM_ZERO
2 2003-12-01 00:00:00.002 -123.4  -123.4    -123.4        -123.4          -123.4
3 2003-12-01 00:00:00.003  123.6   123.6     123.6         123.6           123.6
```

Positive precision truncates to the number of digits *after* floating point:

```
>>> t = otp.Ticks(A=[-123.45, 123.45])
>>> t['ROUND1'] = otp.math.round(t['A'], 1)
>>> t['ROUND2'] = otp.math.round(t['A'], 2)
>>> otp.run(t)
                        Time       A  ROUND1  ROUND2
0 2003-12-01 00:00:00.000 -123.45  -123.4 -123.45
1 2003-12-01 00:00:00.001  123.45   123.5  123.45
```

Negative precision truncates to the number of digits *before* floating point
(and the fraction part is dropped in this case):

```
>>> t = otp.Ticks(A=[-123.45, 123.45])
>>> t['ROUND_M1'] = otp.math.round(t['A'], -1)
>>> t['ROUND_M2'] = otp.math.round(t['A'], -2)
>>> otp.run(t)
                        Time       A  ROUND_M1  ROUND_M2
0 2003-12-01 00:00:00.000 -123.45    -120.0    -100.0
1 2003-12-01 00:00:00.001  123.45     120.0     100.0
```

Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity in all cases:

```
>>> t = otp.Ticks(A=[otp.inf, -otp.inf, otp.nan])
>>> t['ROUND_0'] = otp.math.round(t['A'])
>>> t['ROUND_P2'] = otp.math.round(t['A'], 2)
>>> t['ROUND_M2'] = otp.math.round(t['A'], -2)
>>> otp.run(t)
                     Time    A  ROUND_0  ROUND_P2  ROUND_M2
0 2003-12-01 00:00:00.000  inf      inf       inf       inf
1 2003-12-01 00:00:00.001 -inf     -inf      -inf      -inf
2 2003-12-01 00:00:00.002  NaN      NaN       NaN       NaN
```
