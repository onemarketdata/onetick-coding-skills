# otp.Operation.decimal.cmp

### ``cmp(other, eps)``

Compare two decimal values according to `eps` relative difference.

This function returns 0 if column == other, 1 if column > other, and -1 if column < other.

If both values are NaN, the result is 0.
If only one value is NaN, NaN is treated as less than any non-NaN value.

Two numbers are considered to be equal if:

* `abs(column - other) <= 1e-12` (absolute tolerance; useful near zero)
* or `abs(column - other) / max(1, max(abs(column), abs(other))) <= eps` (relative tolerance).

`eps` is a relative epsilon (scale-dependent), not an absolute difference.

* **Parameters:**
  * **other** (*Operation* *or* *decimal*) – column or value to compare with
  * **eps** (*Operation* *or* *decimal*) – column or value with relative difference
* **Returns:**
  **result** – 0 if column == other, 1 if column > other, and -1 if column < other.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(
...     X=[otp.decimal(2.17), otp.decimal(2.17), otp.decimal(10.31841), otp.decimal(3.141593), otp.decimal(5)],
...     OTHER=[otp.decimal(2.1), otp.decimal(2.1), otp.decimal(10.32841), otp.decimal(3.14), otp.decimal(6)],
...     EPS=[0.01, 0.1, 0.1, 0.0001, 0.01]
... )
>>> data['Y'] = data['X'].decimal.cmp(data['OTHER'], data['EPS'])
>>> otp.run(data)  
                     Time          X     OTHER     EPS    Y
0 2003-12-01 00:00:00.000   2.170000   2.10000  0.0100  1.0
1 2003-12-01 00:00:00.001   2.170000   2.10000  0.1000  0.0
2 2003-12-01 00:00:00.002  10.318410  10.32841  0.1000  0.0
3 2003-12-01 00:00:00.003   3.141593   3.14000  0.0001  1.0
4 2003-12-01 00:00:00.004   5.000000   6.00000  0.0100 -1.0
```
