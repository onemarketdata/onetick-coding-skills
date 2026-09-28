# onetick.py.Operation._\_or_\_

#### Operation.\_\_or_\_(other)

Return logical `or` in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where((t['A'] == 1) | (t['A'] == 2))
>>> otp.run(t)[['A']]
   A
0  1
1  2
```
