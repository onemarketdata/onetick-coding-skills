# otp.Operation.map

#### ``Operation.map(arg, default=None)``

Map values of the column to new values according to the mapping in `arg`.
If the value is not in the mapping, it is set to the `default` value.
If `default` value is not set, it is set to default value for the column type.

* **Parameters:**
  * **arg** (*dict*) – Mapping from old values to new values.
    All values must have the same type, compatible with the column type.
  * **default** (*simple value* *or* *Column* *or* *Operation*) – Default value if no mapping is found in `arg`.
    By default, it is set to default value for the column type.
    (0 for numbers, empty string for strings, etc.)
* **Return type:**
  `Operation`

##### Examples

```
>>> t = otp.Ticks(A=[1, 2, 3, 4, 5])
>>> t['B'] = t['A'].map({1: 10, 2: 20, 3: 30})
>>> otp.run(t)
                     Time  A   B
0 2003-12-01 00:00:00.000  1  10
1 2003-12-01 00:00:00.001  2  20
2 2003-12-01 00:00:00.002  3  30
3 2003-12-01 00:00:00.003  4   0
4 2003-12-01 00:00:00.004  5   0
```

Example with `default` parameter set:

```
>>> t = otp.Ticks(A=[1, 2, 3, 4, 5])
>>> t['B'] = t['A'].map({1: 10, 2: 20, 3: 30}, default=-1)
>>> otp.run(t)
                     Time  A   B
0 2003-12-01 00:00:00.000  1  10
1 2003-12-01 00:00:00.001  2  20
2 2003-12-01 00:00:00.002  3  30
3 2003-12-01 00:00:00.003  4  -1
4 2003-12-01 00:00:00.004  5  -1
```

`default` parameter can also be an ``Operation`` or ``Column``.
For example, to keep unmapped values:

```
>>> t = otp.Ticks(A=[1, 2, 3, 4, 5])
>>> t['B'] = t['A'].map({1: 10, 2: 20, 3: 30}, default=t['A'])
>>> otp.run(t)
                     Time  A   B
0 2003-12-01 00:00:00.000  1  10
1 2003-12-01 00:00:00.001  2  20
2 2003-12-01 00:00:00.002  3  30
3 2003-12-01 00:00:00.003  4   4
4 2003-12-01 00:00:00.004  5   5
```
