# otp.Operation.str.substr

### ``substr(start, n_bytes=None, rtrim=False)``

Return `n_bytes` characters starting from `start`.

For a positive `start` return `num_bytes` of the string, starting from the position specified by
`start` (0-based).
For a negative `start`, the position is counted from the end of the string.
If the `n_bytes` parameter is omitted, returns the part of the input string
starting at `start` till the end of the string.

* **Parameters:**
  * **start** (*int* *or* *Column* *or* *Operation*) – Index of first symbol in substring
  * **n_bytes** (*int* *or* *Column* *or* *Operation*) – Number of bytes in substring
  * **rtrim** (*bool*) – If set to `True`, original string will be trimmed from the right side
    before getting the substring, this can be useful with negative `start` index.
* **Returns:**
  Substring of string (`n_bytes` length starting with `start`).
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['abcdef', '12345     '], START_INDEX=[2, 1], N=[2, 3])
>>> data['FIRST_3'] = data['X'].str.substr(0, 3)
>>> data['LAST_3'] = data['X'].str.substr(-3, rtrim=True)
>>> data['CENTER'] = data['X'].str.substr(data['START_INDEX'], data['N'])
>>> otp.run(data)
                     Time       X  START_INDEX  N FIRST_3 LAST_3 CENTER
0 2003-12-01 00:00:00.000  abcdef            2  2     abc    def     cd
1 2003-12-01 00:00:00.001   12345            1  3     123    345    234
```
