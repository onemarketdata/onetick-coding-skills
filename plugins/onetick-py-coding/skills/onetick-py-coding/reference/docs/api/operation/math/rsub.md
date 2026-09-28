# onetick.py.Operation._\_rsub_\_

#### Operation.\_\_rsub_\_(other)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, D=otp.datetime(2022, 5, 12))
>>> t['A'] -= t['B']
>>> t['B'] -= 1
>>> t['D'] -= otp.Day(1)
>>> otp.run(t)[['A', 'B', 'D']]
     A    B          D
0 -1.3  1.3 2022-05-11
```

##### SEE ALSO
``__sub__``
