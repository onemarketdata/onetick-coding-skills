# otp.math.ceil

### ``ceil(value)``

Returns a float value representing the smallest number that is greater than or equal to the `value`.

* **Parameters:**
  **value** (int, float, ``Operation``, ``Column``)
* **Return type:**
  ``Operation``

#### NOTE
Rounding ``otp.nan`` returns NaN
and rounding ``otp.inf`` returns Infinity.

##### Examples

```
>>> data = otp.Ticks(A=[-1.7, -1.5, -1.2, -1, 0 , 1, 1.2, 1.5, 1.7, -otp.inf, otp.inf, otp.nan])
>>> data['CEIL'] = otp.math.ceil(data['A'])
>>> otp.run(data)
                      Time     A  CEIL
0  2003-12-01 00:00:00.000  -1.7  -1.0
1  2003-12-01 00:00:00.001  -1.5  -1.0
2  2003-12-01 00:00:00.002  -1.2  -1.0
3  2003-12-01 00:00:00.003  -1.0  -1.0
4  2003-12-01 00:00:00.004   0.0   0.0
5  2003-12-01 00:00:00.005   1.0   1.0
6  2003-12-01 00:00:00.006   1.2   2.0
7  2003-12-01 00:00:00.007   1.5   2.0
8  2003-12-01 00:00:00.008   1.7   2.0
9  2003-12-01 00:00:00.009  -inf  -inf
10 2003-12-01 00:00:00.010   inf   inf
11 2003-12-01 00:00:00.011   NaN   NaN
```
