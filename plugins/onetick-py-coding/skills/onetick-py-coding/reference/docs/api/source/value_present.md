# otp.Source.value_present

#### ``Source.value_present(field, values, discard_on_match=False, inplace=False)``

Propagates ticks based on whether the value of the `field` contains one of the list of `values`.

* **Parameters:**
  * **field** (str, ``Column``) – Name of the field (must be present in the input tick descriptor).
  * **values** (*str* *,* *list* **[*str* *]*) – A set of values that are searched for in the value of the `field`.
  * **discard_on_match** (*bool*) – When set to `True` only ticks that did not match the filter are propagated,
    otherwise ticks that satisfy the filter condition are propagated.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Select ticks that have specified values in field A:

```
>>> data = otp.Ticks(A=[1, 2, 3, 4, 5])
>>> data = data.value_present(field='A', values=[2, 3])
>>> otp.run(data)
                     Time  A
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.002  3
```

Use parameter `discard_on_match` to filter values *out*:

```
>>> data = otp.Ticks(A=[1, 2, 3, 4, 5])
>>> data = data.value_present(field='A', values=[2, 3], discard_on_match=True)
>>> otp.run(data)
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.003  4
2 2003-12-01 00:00:00.004  5
```

##### SEE ALSO
**VALUE_PRESENT** OneTick event processor
``onetick.py.Operation.isin()``
