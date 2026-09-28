# otp.agg.mean (alias for otp.agg.average)

### ``mean(*args, **kwargs)``

Implement average aggregation.

##### Examples

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'RESULT': otp.agg.mean('X')})
>>> otp.run(data)
        Time  RESULT
0 2003-12-04     2.5
```

##### SEE ALSO
``average()``

**AVERAGE** OneTick event processor

