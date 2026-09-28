# onetick.py.Operation._\_add_\_

#### Operation.\_\_add_\_(other)

Return the sum of column and `other` value.

* **Parameters:**
  **other** (int, float, str, `offset`, ``onetick.py.Column``)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, C='c', D=otp.datetime(2022, 5, 12))
>>> t['A'] = t['A'] + t['B']
>>> t['B'] = t['B'] + 1
>>> t['C'] = t['C'] + '_suffix'
>>> t['D'] = t['D'] + otp.Day(1)
>>> otp.run(t)[['A', 'B', 'C', 'D']]
     A    B         C          D
0  3.3  3.3  c_suffix 2022-05-13
```
