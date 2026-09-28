# onetick.py.Operation._\_invert_\_

#### Operation.\_\_invert_\_()

Return inversion of filter operation.

##### Examples

```
>>> t = otp.Ticks(A=range(4))
>>> t = t.where(~(t['A'] > 1))
>>> otp.run(t)[['A']]
   A
0  0
1  1
```
