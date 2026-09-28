# otp.Operation.str.endswith

### ``endswith(value)``

Checks if the Operation ends with a string.

* **Parameters:**
  **value** (*str* *or* *Column* *or* *Operation*) – String to check if starts with it.

##### Examples

```
>>> data = otp.Ticks(X=['baaaa', 'bbbbb', 'cbbc', 'c'], Y=['ba', 'bbb', 'c', 'cc'])
>>> data['ENDSWITH_CONST'] = data['X'].str.endswith('bb')
>>> data['ENDSWITH_Y'] = data['X'].str.endswith(data['Y'])
>>> otp.run(data)
                     Time      X    Y  ENDSWITH_CONST  ENDSWITH_Y
0 2003-12-01 00:00:00.000  baaaa   ba             0.0         0.0
1 2003-12-01 00:00:00.001  bbbbb  bbb             1.0         1.0
2 2003-12-01 00:00:00.002   cbbc    c             0.0         1.0
3 2003-12-01 00:00:00.003      c   cc             0.0         0.0
```
