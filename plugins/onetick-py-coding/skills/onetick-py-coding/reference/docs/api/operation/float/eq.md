# otp.Operation.float.eq

### ``eq(other, delta)``

Compare two double values between themselves according to `delta` relative difference.
Calculated as `abs(column - other) <= delta`.

* **Parameters:**
  * **other** (*Operation* *,* *float*) – column or value to compare with
  * **delta** (*Operation* *,* *float*) – column or value with relative difference
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=[1, 2.17, 10.31841, 3.141593, 6],
...                  OTHER=[1.01, 2.1, 10.32841, 3.14, 5],
...                  DELTA=[0, 1, 0.1, 0.01, 0.001])
>>> data["Y"] = (1 + data["X"] - 1).float.eq(data["OTHER"], data["DELTA"])
>>> otp.run(data)
                     Time          X     OTHER  DELTA    Y
0 2003-12-01 00:00:00.000   1.000000   1.01000  0.000  0.0
1 2003-12-01 00:00:00.001   2.170000   2.10000  1.000  1.0
2 2003-12-01 00:00:00.002  10.318410  10.32841  0.100  1.0
3 2003-12-01 00:00:00.003   3.141593   3.14000  0.010  1.0
4 2003-12-01 00:00:00.004   6.000000   5.00000  0.001  0.0
```

##### SEE ALSO
``cmp``
