# otp.Operation.str.first

### ``first(count=1)``

Returns first `count` symbols.

* **Parameters:**
  **count** (*int* *or* *Column* *or* *Operation*) – Number of first symbols to return. Default: 1

##### Examples

```
>>> data = otp.Ticks(X=['abc', 'bac', 'cba'], Y=[3, 1, 10])
>>> data['FIRST'] = data['X'].str.first()
>>> data['FIRST_Y'] = data['X'].str.first(data['Y'])
>>> otp.run(data)
                     Time    X   Y FIRST FIRST_Y
0 2003-12-01 00:00:00.000  abc   3     a     abc
1 2003-12-01 00:00:00.001  bac   1     b       b
2 2003-12-01 00:00:00.002  cba  10     c     cba
```
