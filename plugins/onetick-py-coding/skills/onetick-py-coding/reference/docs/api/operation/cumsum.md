# otp.Column.cumsum

#### ``Column.cumsum()``

Cumulative sum of the column.

Can only be used when creating or updating column.

##### Examples

```
>>> t = otp.Ticks({'A': [1, 2, 3]})
>>> t['X'] = t['A'].cumsum()
>>> otp.run(t)
                     Time  A  X
0 2003-12-01 00:00:00.000  1  1
1 2003-12-01 00:00:00.001  2  3
2 2003-12-01 00:00:00.002  3  6
```
