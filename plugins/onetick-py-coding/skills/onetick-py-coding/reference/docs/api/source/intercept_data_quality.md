# otp.Source.intercept_data_quality

#### ``Source.intercept_data_quality(inplace=False)``

This method removes data quality messages, thus preventing them from delivery to the client application.

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
>>> data = data.insert_data_quality_event('OK')
>>> data = data.show_data_quality()
>>> otp.run(data)
                     Time  DATA_QUALITY_TYPE DATA_QUALITY_NAME
0 2003-12-01 00:00:00.000                  0                OK
1 2003-12-01 00:00:00.001                  0                OK
2 2003-12-01 00:00:00.002                  0                OK
```

Intercepting data quality events will remove them from the data flow:

```
>>> data = otp.Ticks({'A': [1, 2, 3]})
>>> data = data.insert_data_quality_event('OK')
>>> data = data.intercept_data_quality()
>>> data = data.show_data_quality()
>>> otp.run(data)  
Empty DataFrame
...
```

##### SEE ALSO
**INTERCEPT_DATA_QUALITY** OneTick event processor

``insert_data_quality_event()``

``show_data_quality()``

