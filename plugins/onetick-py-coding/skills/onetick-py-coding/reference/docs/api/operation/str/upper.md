# otp.Operation.str.upper

### ``upper()``

Converts a string to upper case.

* **Returns:**
  Uppercased string
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['HeLlO', 'wOrLd!'])
>>> data['UP'] = data['X'].str.upper()
>>> otp.run(data)
                     Time       X      UP
0 2003-12-01 00:00:00.000   HeLlO   HELLO
1 2003-12-01 00:00:00.001  wOrLd!  WORLD!
```
