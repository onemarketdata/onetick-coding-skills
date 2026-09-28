# onetick.py.Operation._\_ge_\_

#### Operation.\_\_ge_\_(other)

Return >= in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where(t['A'] >= 2)
>>> otp.run(t)[['A']]
   A
0  2
1  3
```
