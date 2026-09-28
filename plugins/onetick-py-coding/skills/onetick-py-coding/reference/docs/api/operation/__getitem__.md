# otp.Column._\_getitem_\_

#### Column.\_\_getitem_\_(item)

Provides an ability to get values from future or past ticks.

- Negative values refer to past ticks
- Zero to current tick
- Positive - future ticks

Boundary values will be defaulted. For instance for `item=-1` first tick value will be defaulted
(there is no tick before first tick)

* **Parameters:**
  **item** (*int*) – number of ticks to look back/forward
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks({'A': [1, 2, 3]})
>>> data['PAST1'] = data['A'][-1]
>>> data['PAST2'] = data['A'][-2]
>>> data['FUTURE1'] = data['A'][1]
>>> data['FUTURE2'] = data['A'][2]
>>> otp.run(data)
                     Time  A  PAST1  PAST2  FUTURE1  FUTURE2
0 2003-12-01 00:00:00.000  1      0      0        2        3
1 2003-12-01 00:00:00.001  2      1      0        3        0
2 2003-12-01 00:00:00.002  3      2      1        0        0
```
