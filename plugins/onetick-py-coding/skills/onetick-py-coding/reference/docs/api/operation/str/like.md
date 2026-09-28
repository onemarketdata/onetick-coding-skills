# otp.Operation.str.like

### ``like(pattern)``

Check if the value is matched with SQL-like `pattern`.

* **Parameters:**
  **pattern** (str or symbol parameter (``_SymbolParamColumn``)) – 

  Pattern to match the value with.
  The pattern can contain usual text characters and two special ones:
  * `%` represents zero or more characters
  * `_` represents a single character

  Use backslash `\` character to escape these special characters.
* **Returns:**
  `True` if the match was successful, `False` otherwise.
  Note that boolean Operation is converted to float if added as a column.
* **Return type:**
  `Operation`

##### Examples

Use `%` character to specify any number of characters:

```
>>> data = otp.Ticks(X=['a', 'ab', 'b_', 'b%'])
>>> data['LIKE'] = data['X'].str.like('a%')
>>> otp.run(data)
                     Time   X  LIKE
0 2003-12-01 00:00:00.000   a   1.0
1 2003-12-01 00:00:00.001  ab   1.0
2 2003-12-01 00:00:00.002  b_   0.0
3 2003-12-01 00:00:00.003  b%   0.0
```

Use `_` special character to specify a single character:

```
>>> data = otp.Ticks(X=['a', 'ab', 'b_', 'b%'])
>>> data['LIKE'] = data['X'].str.like('a_')
>>> otp.run(data)
                     Time   X  LIKE
0 2003-12-01 00:00:00.000   a   0.0
1 2003-12-01 00:00:00.001  ab   1.0
2 2003-12-01 00:00:00.002  b_   0.0
3 2003-12-01 00:00:00.003  b%   0.0
```

Use backslash `\` character to escape special characters:

```
>>> data = otp.Ticks(X=['a', 'ab', 'b_', 'b%'])
>>> data['LIKE'] = data['X'].str.like(r'b\_')
>>> otp.run(data)
                     Time   X  LIKE
0 2003-12-01 00:00:00.000   a   0.0
1 2003-12-01 00:00:00.001  ab   0.0
2 2003-12-01 00:00:00.002  b_   1.0
3 2003-12-01 00:00:00.003  b%   0.0
```

This function can be used to filter out ticks:

```
>>> data = otp.Ticks(X=['a', 'ab', 'b_', 'b%'])
>>> data = data.where(data['X'].str.like('a%'))
>>> otp.run(data)
                     Time   X
0 2003-12-01 00:00:00.000   a
1 2003-12-01 00:00:00.001  ab
```

`pattern` can only be a constant expression, like string or symbol parameter:

```
>>> data = otp.Ticks(X=['a', 'ab', 'b_', 'b%'])
>>> data['LIKE'] = data['X'].str.like(data.Symbol['PATTERN', str])
>>> otp.run(data, symbols=otp.Tick(SYMBOL_NAME='COMMON::AAPL', PATTERN='_'))['COMMON::AAPL']
                     Time   X  LIKE
0 2003-12-01 00:00:00.000   a   1.0
1 2003-12-01 00:00:00.001  ab   0.0
2 2003-12-01 00:00:00.002  b_   0.0
3 2003-12-01 00:00:00.003  b%   0.0
```
