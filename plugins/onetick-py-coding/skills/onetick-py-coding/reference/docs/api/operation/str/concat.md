# otp.Operation.str.concat

### ``concat(other)``

Returns a string that is the result of concatenating to `others`.

* **Parameters:**
  **other** (*str* *or* *Column* *or* *Operation*) – String to concatenate with.

##### Examples

```
>>> data = otp.Ticks(X=['X1', 'X2', 'X3'], Y=['Y1', 'Y2', 'Y3'])
>>> data['X_WITH_CONST_SUFFIX'] = data['X'].str.concat('_suffix')
>>> data['X_WTH_Y'] = data['X'].str.concat(data['Y'])
>>> otp.run(data)
                     Time   X   Y X_WITH_CONST_SUFFIX X_WTH_Y
0 2003-12-01 00:00:00.000  X1  Y1           X1_suffix    X1Y1
1 2003-12-01 00:00:00.001  X2  Y2           X2_suffix    X2Y2
2 2003-12-01 00:00:00.002  X3  Y3           X3_suffix    X3Y3
```
