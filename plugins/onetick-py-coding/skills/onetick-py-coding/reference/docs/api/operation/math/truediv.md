# onetick.py.Operation._\_truediv_\_

#### Operation.\_\_truediv_\_(other)

Divide column by `other` value.

* **Parameters:**
  **other** (int, float, ``onetick.py.Column``)

##### Examples

```
>>> t = otp.Tick(A=1, B=2.3)
>>> t['A'] = t['A'] / t['B']
>>> t['B'] = t['B'] / 2
>>> otp.run(t)[['A', 'B']]
          A     B
0  0.434783  1.15
```
