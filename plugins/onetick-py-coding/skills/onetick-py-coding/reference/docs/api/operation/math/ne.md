# onetick.py.Operation._\_ne_\_

#### Operation.\_\_ne_\_(other)

Return inequality in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where((t['A'] != 1))
>>> otp.run(t)[['A']]
   A
0  0
1  2
2  3
```
