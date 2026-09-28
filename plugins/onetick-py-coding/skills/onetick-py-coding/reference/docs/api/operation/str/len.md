# otp.Operation.str.len

### ``len()``

Get the length of a string.

* **Returns:**
  The length of the string.
  If a null-character (byte with value `0`) is present in the string,
  its position (0-based) is returned.
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['hello', 'world!'])
>>> data['LEN'] = data['X'].str.len()
>>> otp.run(data)
                     Time       X  LEN
0 2003-12-01 00:00:00.000   hello    5
1 2003-12-01 00:00:00.001  world!    6
```
