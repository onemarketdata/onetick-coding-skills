# otp.Operation.str.startswith

### ``startswith(value)``

Checks if the Operation starts with a string.

* **Parameters:**
  **value** (*str* *or* *Column* *or* *Operation*) – String to check if starts with it.

##### Examples

```
>>> data = otp.Ticks(X=['baaaa', 'bbbbb', 'cbbc'], Y=['ba', 'abb', 'c'])
>>> data['STARTSWITH_CONST'] = data['X'].str.startswith('bb')
>>> data['STARTSWITH_Y'] = data['X'].str.startswith(data['Y'])
>>> otp.run(data)
                     Time      X    Y  STARTSWITH_CONST  STARTSWITH_Y
0 2003-12-01 00:00:00.000  baaaa   ba               0.0           1.0
1 2003-12-01 00:00:00.001  bbbbb  abb               1.0           0.0
2 2003-12-01 00:00:00.002   cbbc    c               0.0           1.0
```
