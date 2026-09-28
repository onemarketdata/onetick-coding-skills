# onetick.py.Operation._\_and_\_

#### Operation.\_\_and_\_(other)

Return logical `and` in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=[1, 1], B=[1, 2])
>>> t = t.where((t['A'] == 1) & (t['B'] == 1))
>>> otp.run(t)[['A', 'B']]
   A  B
0  1  1
```
