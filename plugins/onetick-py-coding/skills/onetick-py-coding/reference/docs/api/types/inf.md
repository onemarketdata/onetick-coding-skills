# otp.inf

### *class* inf

Bases:

Object that represents infinity value.
Can be used anywhere where float value is expected.

##### Examples

```
>>> t = otp.Ticks({'A': [1.1, 2.2, otp.inf]})
>>> t['B'] = otp.inf
>>> t['C'] = t['A'] / 0
>>> t['D'] = t['A'] - otp.inf
>>> otp.run(t)
                     Time    A    B    C    D
0 2003-12-01 00:00:00.000  1.1  inf  inf -inf
1 2003-12-01 00:00:00.001  2.2  inf  inf -inf
2 2003-12-01 00:00:00.002  inf  inf  inf  NaN
```
