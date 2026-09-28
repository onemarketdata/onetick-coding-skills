# onetick.py.Operation._\_radd_\_

#### Operation.\_\_radd_\_(other)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, C='c', D=otp.datetime(2022, 5, 12))
>>> t['A'] += t['B']
>>> t['B'] += 1
>>> t['C'] += '_suffix'
>>> t['D'] += otp.Day(1)
>>> otp.run(t)[['A', 'B', 'C', 'D']]
     A    B         C          D
0  3.3  3.3  c_suffix 2022-05-13
```

##### SEE ALSO
``__add__``
