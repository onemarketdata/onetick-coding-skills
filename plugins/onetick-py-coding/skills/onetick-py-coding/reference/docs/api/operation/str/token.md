# otp.Operation.str.token

### ``token(sep=' ', n=0)``

Breaks the value into tokens based on the delimiter `sep`
and returns token at position `n` (zero-based).

If there are not enough tokens to get the one at position `n`, then empty string is returned.

* **Parameters:**
  * **sep** (*str* *or* *Column* *or* *Operation*) – The delimiter, which must be a single character used to split the string into tokens.
  * **n** (*int* *,* *Operation*) – Token index to return. For a negative `n`, count from the end instead of the beginning.
    If index is out of range, then empty string is returned.
* **Returns:**
  token at position `n` or empty string.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Tick(X='US_COMP::TRD')
>>> data['Y'] = data['X'].str.token(':', -1)
>>> otp.run(data)
        Time              X    Y
0 2003-12-01  US_COMP::TRD  TRD
```

Other columns can be used as parameters too:

```
>>> data = otp.Tick(X='US_COMP::TRD', SEP=':', N=-1)
>>> data['Y'] = data['X'].str.token(data['SEP'], data['N'])
>>> otp.run(data)
        Time              X SEP  N    Y
0 2003-12-01  US_COMP::TRD   : -1  TRD
```

If index is out of range, then empty string is returned:

```
>>> data = otp.Tick(X='US_COMP::TRD')
>>> data['Y'] = data['X'].str.token(':', 999)
>>> otp.run(data)
        Time              X  Y
0 2003-12-01  US_COMP::TRD
```
