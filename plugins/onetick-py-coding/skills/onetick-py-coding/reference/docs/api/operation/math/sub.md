# onetick.py.Operation._\_sub_\_

#### Operation.\_\_sub_\_(other)

Subtract `other` value from column.

* **Parameters:**
  **other** (int, float, `offset`, ``onetick.py.Column``)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, D=otp.datetime(2022, 5, 12))
>>> t['A'] = t['A'] - t['B']
>>> t['B'] = t['B'] - 1
>>> t['D'] = t['D'] - otp.Day(1)
>>> otp.run(t)[['A', 'B', 'D']]
     A    B          D
0 -1.3  1.3 2022-05-11
```
