# otp.Source.intercept_symbol_errors

#### ``Source.intercept_symbol_errors(inplace=False)``

This method removes removes per-symbol errors, thus preventing them from delivery to the client application.

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

By default data quality events are not showed, use this method to see them:

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

Intercepting data quality events will remove them from the data flow:

```
>>> data = otp.Ticks({'A': [1, 2, 3]})
>>> data = data.throw('WRONG', scope='symbol')
>>> data = data.intercept_symbol_errors()
>>> data = data.show_symbol_errors()
>>> otp.run(data)  
Empty DataFrame
...
```

##### SEE ALSO
**INTERCEPT_SYMBOL_ERRORS** OneTick event processor

``show_symbol_errors()``

``onetick.py.Source.throw()``

