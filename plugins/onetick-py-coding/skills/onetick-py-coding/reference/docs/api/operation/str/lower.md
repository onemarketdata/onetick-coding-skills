# otp.Operation.str.lower

### ``lower()``

Convert a string to lower case.

* **Returns:**
  Lowercased string
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['HeLlO', 'wOrLd!'])
>>> data['LOW'] = data['X'].str.lower()
>>> otp.run(data)
                     Time       X     LOW
0 2003-12-01 00:00:00.000   HeLlO   hello
1 2003-12-01 00:00:00.001  wOrLd!  world!
```
