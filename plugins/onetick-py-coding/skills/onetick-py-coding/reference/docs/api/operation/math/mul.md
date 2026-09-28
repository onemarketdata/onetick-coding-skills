# onetick.py.Operation._\_mul_\_

#### Operation.\_\_mul_\_(other)

Multiply column by `other` value.

* **Parameters:**
  **other** (int, float, str, ``onetick.py.Column``)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3, C='c')
>>> t['A'] = t['A'] * t['B']
>>> t['B'] = t['B'] * 2
>>> t['C'] = t['C'] * 3
>>> otp.run(t)[['A', 'B', 'C']]
     A    B    C
0  2.3  4.6  ccc
```
