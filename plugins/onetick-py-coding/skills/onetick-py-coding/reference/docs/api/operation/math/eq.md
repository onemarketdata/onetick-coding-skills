# onetick.py.Operation._\_eq_\_

#### Operation.\_\_eq_\_(other)

Return equality in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where((t['A'] == 1))
>>> otp.run(t)[['A']]
   A
0  1
```
