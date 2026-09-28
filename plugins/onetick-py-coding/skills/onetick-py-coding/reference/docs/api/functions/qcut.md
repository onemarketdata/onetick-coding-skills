# otp.qcut

### ``qcut(column, q, labels=None)``

Quantile-based discretization function (mimics `pandas.qcut`).

* **Parameters:**
  * **column** (``Column``) – Column with numeric data used to build bins.
  * **q** (*int* *or* *list* **[*float* *]*) – 

    When list[float] - array of quantiles, e.g. [0, .25, .5, .75, 1.] for quartiles.

    When int - Number of quantiles. 10 for deciles, 4 for quartiles, etc.
  * **labels** (*list* **[*str* *]*) – Labels used to name resulting bins.
    If not set, bins are numeric intervals like (5.0000000000, 7.5000000000].
* **Return type:**
  object that can be set to ``Column`` via ``__setitem__()``

##### Examples

```
>>> data = otp.Ticks({"X": [10, 3, 5, 6, 7, 1]})
>>> data['bin'] = otp.qcut(data['X'], q=3, labels=['a', 'b', 'c'])
>>> otp.run(data)[['X', 'bin']]
    X bin
0  10   c
1   3   a
2   5   b
3   6   b
4   7   c
5   1   a
```
