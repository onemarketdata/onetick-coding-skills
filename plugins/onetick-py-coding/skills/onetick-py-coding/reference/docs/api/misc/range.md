# otp.range

### ``class range(start, stop)``

Bases: ``object``

Class that expresses OneTick ranges.
For example, if you want to express a range in the .split() method,
then you can use this range.

It has start and stop fields that allow you to define a range.

##### Examples

```
>>> data = otp.Ticks(X=[0.33, -5.1, otp.nan, 9.4])
>>> r1, r2, r3 = data.split(data['X'], [otp.nan, otp.range(0, 100)], default=True)
>>> otp.run(r1)
                     Time   X
0 2003-12-01 00:00:00.002 NaN
>>> otp.run(r2)
                     Time     X
0 2003-12-01 00:00:00.000  0.33
1 2003-12-01 00:00:00.003  9.40
>>> otp.run(r3)
                     Time    X
0 2003-12-01 00:00:00.001 -5.1
```

##### SEE ALSO
``split()``
