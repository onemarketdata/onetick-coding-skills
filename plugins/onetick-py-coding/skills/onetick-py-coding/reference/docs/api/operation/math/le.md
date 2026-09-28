# onetick.py.Operation._\_le_\_

#### Operation.\_\_le_\_(other)

Return <= in filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where(t['A'] <= 2)
>>> otp.run(t)[['A']]
   A
0  0
1  1
2  2
```
