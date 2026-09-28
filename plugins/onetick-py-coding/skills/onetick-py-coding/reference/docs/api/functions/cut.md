# otp.cut

### ``cut(column, bins, labels=None)``

Bin values into discrete intervals (mimics `pandas.cut`).

* **Parameters:**
  * **column** (``Column``) – Column with numeric data used to build bins.
  * **bins** (*int* *or* *list* **[*float* *]*) – 

    When list[float] - defines the bin edges.

    When int - Defines the number of equal-width bins in the range of x.
  * **labels** (*list* **[*str* *]*) – Labels used to name resulting bins.
    If not set, bins are numeric intervals like (5.0000000000, 7.5000000000].
* **Return type:**
  object that can be set to ``Column`` via ``__setitem__()``

##### Examples

```
>>> data = otp.Ticks({"X": [9, 8, 5, 6, 7, 0, ]})
>>> data['bin'] = otp.cut(data['X'], bins=3, labels=['a', 'b', 'c'])
>>> otp.run(data)[['X', 'bin']]
   X bin
0  9   c
1  8   c
2  5   b
3  6   b
4  7   c
5  0   a
```
