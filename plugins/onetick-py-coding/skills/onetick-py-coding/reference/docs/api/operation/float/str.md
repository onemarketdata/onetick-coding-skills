# otp.Operation.float.str

### ``str(length=10, precision=6)``

Converts float to str.

Converts number to string with given `length` and `precision`.
The specified `length` should be greater than or equal
to the part of the number before the decimal point plus the number’s sign (if any).

If `length` is specified as an int, the method will return strings with `length` characters,
if `length` is specified as a column, the method will return string default (64 characters) length.

* **Parameters:**
  * **length** (*Operation* *or* *int*) – Length of the string.
  * **precision** (*Operation* *or* *int*) – Number of symbols after dot.
* **Returns:**
  **result** – String representation of float value.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=[1, 2.17, 10.31861, 3.141593, otp.nan, otp.inf, -otp.inf])
>>> data["Y"] = data["X"].float.str(15, 3)
>>> otp.run(data)
                     Time          X       Y
0 2003-12-01 00:00:00.000   1.000000   1.000
1 2003-12-01 00:00:00.001   2.170000   2.170
2 2003-12-01 00:00:00.002  10.318610  10.319
3 2003-12-01 00:00:00.003   3.141593   3.142
4 2003-12-01 00:00:00.004        NaN     nan
5 2003-12-01 00:00:00.005        inf     inf
6 2003-12-01 00:00:00.006       -inf    -inf
```

Parameters `length` and `precision` can also be taken from the columns:

```
>>> data = otp.Ticks(X=[1, 2.17, 10.31841, 3.141593],
...                  LENGTH=[2, 3, 4, 5],
...                  PRECISION=[5, 5, 3, 3])
>>> data["Y"] = data["X"].float.str(data["LENGTH"], data["PRECISION"])
>>> otp.run(data)
                     Time          X  LENGTH  PRECISION      Y
0 2003-12-01 00:00:00.000   1.000000       2          5      1
1 2003-12-01 00:00:00.001   2.170000       3          5    2.2
2 2003-12-01 00:00:00.002  10.318410       4          3   10.3
3 2003-12-01 00:00:00.003   3.141593       5          3  3.142
```
