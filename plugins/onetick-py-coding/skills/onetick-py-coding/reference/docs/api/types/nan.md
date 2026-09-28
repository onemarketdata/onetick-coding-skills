# otp.nan

### *class* nan

Bases:

Object that represents NaN (not a number) float value.
Can be used anywhere where float value is expected.

##### Examples

```
>>> t = otp.Ticks({'A': [1.1, 2.2, otp.nan]})
>>> t['B'] = otp.nan
>>> t['C'] = t['A'] / 0
>>> t['D'] = t['A'] + otp.nan
>>> otp.run(t)
                     Time    A   B    C   D
0 2003-12-01 00:00:00.000  1.1 NaN  inf NaN
1 2003-12-01 00:00:00.001  2.2 NaN  inf NaN
2 2003-12-01 00:00:00.002  NaN NaN  NaN NaN
```
