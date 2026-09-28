# otp.Source.insert_data_quality_event

#### ``Source.insert_data_quality_event(data_quality, where=None, insert_before=True, inplace=False)``

Insert data quality events into the data flow.

* **Parameters:**
  * **data_quality** (*str*) – The type of data quality event.
    Must be one of the supported data quality event types.
  * **where** (str or ``Operation``) – Specifies a criterion for the selection of ticks whose arrival results in generation of a data quality event.
    If this expression returns True, a data quality event will be inserted into a time series.
    The expression can also be empty (default), in which case each input tick generates a data quality event.
  * **insert_before** (*bool*) – If True (default),
    generated data quality event tick will be inserted before the input tick that triggered its creation,
    otherwise generated data quality event will be inserted after that input tick.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Insert OK data quality event before each tick:

```
>>> data = otp.Ticks({'A': [1, 2, 3]})
>>> data = data.insert_data_quality_event('OK')
>>> otp.run(data)
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

By default data quality events are not showed,
use ``show_data_quality()`` method to see them:

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

Use `where` parameter to specify the condition when to insert ticks:

```
>>> data = otp.Ticks({'SIZE': [1, 200, 3]})
>>> data = data.insert_data_quality_event('MISSING', where=data['SIZE'] > 100)
>>> data = data.show_data_quality()
>>> otp.run(data)
                     Time  DATA_QUALITY_TYPE DATA_QUALITY_NAME
0 2003-12-01 00:00:00.001                  2           MISSING
```

##### SEE ALSO
**INSERT_DATA_QUALITY_EVENT** OneTick event processor

``show_data_quality()``

``intercept_data_quality()``

