# otp.Source.show_symbol_errors

#### ``Source.show_symbol_errors(inplace=False)``

This method propagates a tick representing per-symbol error.

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

By default symbol errors are not showed, use this method to see them:

```
>>> data = otp.Ticks({'A': [1, 2, 3]})
>>> data = data.throw('WRONG', scope='symbol')
>>> data = data.show_symbol_errors()
>>> otp.run(data)
        Time  ERROR_CODE ERROR_MESSAGE
0 2003-12-01           1         WRONG
1 2003-12-01           1         WRONG
2 2003-12-01           1         WRONG
```

##### SEE ALSO
**SHOW_SYMBOL_ERRORS** OneTick event processor

``intercept_symbol_errors()``

``onetick.py.Source.throw()``

