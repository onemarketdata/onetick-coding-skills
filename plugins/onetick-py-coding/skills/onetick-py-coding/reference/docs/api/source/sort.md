# otp.Source.sort

#### ``Source.sort(by, ascending=True, inplace=False)``

Sort ticks by columns.

* **Parameters:**
  * **by** (*str* *,* *Column* *or* *list* *of* *them*) – Column(s) to sort by. It is possible to pass a list of column, where is the order is important:
    from the left to the right.
  * **ascending** (*bool* *or* *list*) – Order to sort by. If list of columns is specified, then list of ascending values per column is expected.
    (the ``nan`` is the smallest for `float` type fields)
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object
  * **self** (*Source*)
* **Return type:**
  ``Source``

##### Examples

Single column examples

```
>>> data = otp.Ticks({'X':[     94,   5,   34],
...                   'Y':[otp.nan, 3.1, -0.3]})
>>> data = data.sort(data['X'])
>>> otp.run(data)
                     Time   X    Y
0 2003-12-01 00:00:00.001   5  3.1
1 2003-12-01 00:00:00.002  34 -0.3
2 2003-12-01 00:00:00.000  94  NaN
```

```
>>> data = otp.Ticks({'X':[     94,   5,   34],
...                   'Y':[otp.nan, 3.1, -0.3]})
>>> data = data.sort(data['Y'])
>>> otp.run(data)
                     Time   X    Y
0 2003-12-01 00:00:00.000  94  NaN
1 2003-12-01 00:00:00.002  34 -0.3
2 2003-12-01 00:00:00.001   5  3.1
```

Inplace

```
>>> data = otp.Ticks({'X':[     94,   5,   34],
...                   'Y':[otp.nan, 3.1, -0.3]})
>>> data.sort(data['Y'], inplace=True)
>>> otp.run(data)
                     Time   X    Y
0 2003-12-01 00:00:00.000  94 NaN
1 2003-12-01 00:00:00.002  34 -0.3
2 2003-12-01 00:00:00.001  5   3.1
```

Multiple columns

```
>>> data = otp.Ticks({'X':[  5,   6,   3,   6],
...                   'Y':[1.4, 3.1, 9.1, 5.5]})
>>> data = data.sort([data['X'], data['Y']])
>>> otp.run(data)
                     Time  X    Y
0 2003-12-01 00:00:00.002  3  9.1
1 2003-12-01 00:00:00.000  5  1.4
2 2003-12-01 00:00:00.001  6  3.1
3 2003-12-01 00:00:00.003  6  5.5
```

Ascending/descending control

```
>>> data = otp.Ticks({'X':[     94,   5,   34],
...                   'Y':[otp.nan, 3.1, -0.3]})
>>> data = data.sort(data['X'], ascending=False)
>>> otp.run(data)
                     Time   X    Y
0 2003-12-01 00:00:00.000  94  NaN
1 2003-12-01 00:00:00.002  34 -0.3
2 2003-12-01 00:00:00.001   5  3.1
```

```
>>> data = otp.Ticks({'X':[  5,   6,   3,   6],
...                   'Y':[1.4, 3.1, 9.1, 5.5]})
>>> data = data.sort([data['X'], data['Y']], ascending=[True, False])
>>> otp.run(data)
                     Time  X    Y
0 2003-12-01 00:00:00.002  3  9.1
1 2003-12-01 00:00:00.000  5  1.4
2 2003-12-01 00:00:00.003  6  5.5
3 2003-12-01 00:00:00.001  6  3.1
```

##### SEE ALSO
**ORDER_BY** OneTick event processor
