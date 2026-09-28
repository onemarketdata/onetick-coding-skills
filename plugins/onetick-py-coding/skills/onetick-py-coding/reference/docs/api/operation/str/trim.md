# otp.Operation.str.trim

### ``trim()``

Removes white spaces from both sides of the string.

* **Returns:**
  Trimmed string
* **Return type:**
  `Operation`

##### Examples

```
>>> data = otp.Ticks(X=['  Hello', 'World  '])
>>> data['X'] = data['X'].str.trim()
>>> otp.run(data)
                     Time      X
0 2003-12-01 00:00:00.000  Hello
1 2003-12-01 00:00:00.001  World
```

##### SEE ALSO
``ltrim()``, ``rtrim()``
