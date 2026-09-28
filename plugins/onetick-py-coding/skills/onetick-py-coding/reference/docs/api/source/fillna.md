# otp.Source.fillna

#### ``Source.fillna(value=None, columns=None, inplace=False)``

Replace NaN values in floating point type fields with the `value`.

* **Parameters:**
  * **value** (float, ``Operation``) – The value to replace NaN.
    If not specified then the value from the previous tick will be used.
  * **columns** (*list*) – List of strings with column names or ``Column`` objects.
    Only the values in specified columns will be replaced.
    By default the values in all floating point type fields will be replaced.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

By default, the value of the previous tick is used as a value to replace NaN
(for the first tick the previous value do not exist, so it will still be NaN):

```
>>> data = otp.Ticks({'A': [0, 1, 2, 3], 'B': [otp.nan, 2.2, otp.nan, 3.3]})
>>> data = data.fillna()
>>> otp.run(data)
                     Time  A    B
0 2003-12-01 00:00:00.000  0  NaN
1 2003-12-01 00:00:00.001  1  2.2
2 2003-12-01 00:00:00.002  2  2.2
3 2003-12-01 00:00:00.003  3  3.3
```

The value can also be a constant:

```
>>> data = otp.Ticks({'A': [0, 1, 2, 3], 'B': [otp.nan, 2.2, otp.nan, 3.3]})
>>> data = data.fillna(777)
>>> otp.run(data)
                     Time  A     B
0 2003-12-01 00:00:00.000  0  777.0
1 2003-12-01 00:00:00.001  1    2.2
2 2003-12-01 00:00:00.002  2  777.0
3 2003-12-01 00:00:00.003  3    3.3
```

``Operation`` objects can also be used as a `value`:

```
>>> data = otp.Ticks({'A': [0, 1, 2, 3], 'B': [otp.nan, 2.2, otp.nan, 3.3]})
>>> data = data.fillna(data['A'])
>>> otp.run(data)
                     Time  A     B
0 2003-12-01 00:00:00.000  0    0.0
1 2003-12-01 00:00:00.001  1    2.2
2 2003-12-01 00:00:00.002  2    2.0
3 2003-12-01 00:00:00.003  3    3.3
```

Parameter `columns` can be used to specify the columns where values will be replaced:

```
>>> data = otp.Ticks({'A': [0, 1, 2, 3], 'B': [otp.nan, 2.2, otp.nan, 3.3], 'C': [otp.nan, 2.2, otp.nan, 3.3]})
>>> data = data.fillna(columns=['B'])
>>> otp.run(data)
                     Time  A    B    C
0 2003-12-01 00:00:00.000  0  NaN  NaN
1 2003-12-01 00:00:00.001  1  2.2  2.2
2 2003-12-01 00:00:00.002  2  2.2  NaN
3 2003-12-01 00:00:00.003  3  3.3  3.3
```

##### SEE ALSO
``onetick.py.Operation.fillna()``
