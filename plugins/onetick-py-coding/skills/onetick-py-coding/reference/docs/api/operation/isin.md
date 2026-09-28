# otp.Operation.isin

#### ``Operation.isin(*items)``

Check if column’s value is in `items`.

* **Parameters:**
  **items** – Possible string or numeric values to be checked against column’s value.
  Such values can be passed as the function arguments
  or as the list of such values in the first function argument.
* **Returns:**
  Returns an ``onetick.py.Operation`` object with the value of 1.0 if
  the column’s value was found among `items` or 0.0 otherwise.
* **Return type:**
  `Operation`

##### Examples

Passing values as function arguments:

```
>>> data = otp.Ticks(A=['a', 'b', 'c'])
>>> data['B'] = data['A'].isin('a', 'c')
>>> otp.run(data)
                     Time  A    B
0 2003-12-01 00:00:00.000  a  1.0
1 2003-12-01 00:00:00.001  b  0.0
2 2003-12-01 00:00:00.002  c  1.0
```

Passing values as a list:

```
>>> data = otp.Ticks(A=['a', 'b', 'c'])
>>> data['B'] = data['A'].isin(['a', 'c'])
>>> otp.run(data)
                     Time  A    B
0 2003-12-01 00:00:00.000  a  1.0
1 2003-12-01 00:00:00.001  b  0.0
2 2003-12-01 00:00:00.002  c  1.0
```

This function’s result can be used as filter expression:

```
>>> data = otp.Ticks(A=[1, 2, 3, 0])
>>> yes, no = data[data["A"].isin(0, 1)]
>>> otp.run(yes)[["A"]]
   A
0  1
1  0
```

``Columns`` and ``operations`` are also supported:

```
>>> data = otp.Ticks(A=["ab", "cv", "bc", "a", "d"], B=["a", "c", "b", "a", "a"])
>>> yes, no = data[data["A"].isin(data["B"], data["B"] + "b")]
>>> otp.run(yes)[["A", "B"]]
    A  B
0  ab  a
1   a  a
```

##### SEE ALSO
``Source.__getitem__()``
