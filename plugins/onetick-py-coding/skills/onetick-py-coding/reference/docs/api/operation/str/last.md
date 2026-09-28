# otp.Operation.str.last

### ``last(count=1)``

Returns last `count` symbols.

* **Parameters:**
  **count** (*int* *or* *Column* *or* *Operation*) – Number of last symbols to return. Default: 1

##### Examples

```
>>> data = otp.Ticks(X=['abc', 'bac', 'cba'], Y=[3, 1, 9])
>>> data['LAST'] = data['X'].str.last()
>>> data['LAST_Y'] = data['X'].str.last(data['Y'])
>>> otp.run(data)
                     Time    X  Y LAST LAST_Y
0 2003-12-01 00:00:00.000  abc  3    c    abc
1 2003-12-01 00:00:00.001  bac  1    c      c
2 2003-12-01 00:00:00.002  cba  9    a    cba
```
