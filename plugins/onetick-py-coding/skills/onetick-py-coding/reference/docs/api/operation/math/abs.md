# onetick.py.Operation._\_abs_\_

#### Operation.\_\_abs_\_()

Return the absolute value of float or int column.

##### Examples

```
>>> t = otp.Tick(A=-1, B=-2.3)
>>> t['A'] = abs(t['A'])
>>> t['B'] = abs(t['B'])
>>> otp.run(t)[['A', 'B']]
   A    B
0  1  2.3
```
