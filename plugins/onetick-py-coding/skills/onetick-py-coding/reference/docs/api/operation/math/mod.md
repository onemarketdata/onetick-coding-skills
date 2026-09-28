# onetick.py.Operation._\_mod_\_

#### Operation.\_\_mod_\_(other)

Return modulo of division of int column by `other` value.

* **Parameters:**
  **other** (int, ``onetick.py.Column``)

##### Examples

```
>>> t = otp.Tick(A=3, B=3)
>>> t['A'] = t['A'] % t['B']
>>> t['B'] = t['B'] % 2
>>> otp.run(t)[['A', 'B']]
   A  B
0  0  1
```
