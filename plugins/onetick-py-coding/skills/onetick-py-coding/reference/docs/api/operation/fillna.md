# otp.Operation.fillna

#### ``Operation.fillna(value)``

Fill ``nan`` values with `value`.

* **Parameters:**
  **value** (float, int, ``Operation``) – value to use instead ``nan``

##### Examples

Replace NaN values with a constant:

```
>>> data = otp.Ticks({'A': [1, otp.nan, 2]})
>>> data['A'] = data['A'].fillna(100)
>>> otp.run(data)
                     Time      A
0 2003-12-01 00:00:00.000    1.0
1 2003-12-01 00:00:00.001  100.0
2 2003-12-01 00:00:00.002    2.0
```

Replace NaN values with a value from the previous tick:

```
>>> data = otp.Ticks({'A': [1, otp.nan, 2]})
>>> data['A'] = data['A'].fillna(data['A'][-1])
>>> otp.run(data)
                     Time    A
0 2003-12-01 00:00:00.000  1.0
1 2003-12-01 00:00:00.001  1.0
2 2003-12-01 00:00:00.002  2.0
```
