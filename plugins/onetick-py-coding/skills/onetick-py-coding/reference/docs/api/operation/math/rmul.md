# onetick.py.Operation._\_rmul_\_

#### Operation.\_\_rmul_\_(other)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, C='c')
>>> t['A'] *= t['B']
>>> t['B'] *= 2
>>> t['C'] *= 3
>>> otp.run(t)[['A', 'B', 'C']]
     A    B    C
0  2.3  4.6  ccc
```

##### SEE ALSO
``__mul__``
