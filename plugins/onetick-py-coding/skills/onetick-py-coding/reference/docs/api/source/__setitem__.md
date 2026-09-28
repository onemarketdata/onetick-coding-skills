# otp.Source._\_setitem_\_

#### Source.\_\_setitem_\_(key, value)

Add new column to the source or update existing one.

* **Parameters:**
  * **key** (*str*) – The name of the new or existing column.
  * **value** (int, str, float, ``datetime.datetime``, ``datetime.date``,             ``Column``, ``Operation``, ``string``,             ``otp.date``, ``otp.datetime``,             ``nsectime``, ``msectime``) – The new value of the column.
  * **self** (*Source*)

##### Examples

```
>>> data = otp.Tick(A='A')
>>> data['D'] = otp.datetime(2022, 2, 2)
>>> data['X'] = 1
>>> data['Y'] = data['X']
>>> data['X'] = 12345
>>> data['Z'] = data['Y'].astype(str) + 'abc'
>>> otp.run(data)
        Time  A          D      X  Y     Z
0 2003-12-01  A 2022-02-02  12345  1  1abc
```

##### SEE ALSO
**ADD_FIELD** OneTick event processor

**UPDATE_FIELD** OneTick event processor

