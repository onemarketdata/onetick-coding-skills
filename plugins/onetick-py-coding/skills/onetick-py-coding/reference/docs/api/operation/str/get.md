# otp.Operation.str.get

### ``get(i)``

Returns the character at the position indicated by the 0-based index; and empty string,
if position is greater or equal to the length.

* **Parameters:**
  **i** (*int* *or* *Column* *or* *Operation*) – Index of the character to find.

##### Examples

```
>>> data = otp.Ticks(X=['abcdef', '12345     ', 'qw'], GET_INDEX=[2, 1, 0])
>>> data['THIRD'] = data['X'].str.get(2)
>>> data['FROM_INDEX'] = data['X'].str.get(data['GET_INDEX'])
>>> otp.run(data)
                     Time           X  GET_INDEX THIRD FROM_INDEX
0 2003-12-01 00:00:00.000      abcdef          2     c          c
1 2003-12-01 00:00:00.001  12345               1     3          2
2 2003-12-01 00:00:00.002          qw          0                q
```

It is possible to use syntax with indexer to call this method:

```
>>> data = otp.Ticks(X=['abcdef', '12345     ', 'qw'])
>>> data['THIRD'] = data['X'].str[1]
>>> otp.run(data)
                     Time           X THIRD
0 2003-12-01 00:00:00.000      abcdef     b
1 2003-12-01 00:00:00.001  12345          2
2 2003-12-01 00:00:00.002          qw     w
```
