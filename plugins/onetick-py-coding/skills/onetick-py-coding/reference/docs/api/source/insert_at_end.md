# otp.Source.insert_at_end

#### ``Source.insert_at_end(*, propagate_ticks=True, delimiter_name='AT_END', inplace=False)``

This function adds a field `delimiter_name`,
which is set to zero for all inbound ticks
and set to 1 for an additional tick that is generated when the data ends.

The timestamp of the additional tick is set to the query end time.
The values of all fields from the input schema of additional tick are set to default values for each type.

* **Parameters:**
  * **propagate_ticks** (*bool*) – If True (default) this function returns all input ticks and an additionally generated tick,
    otherwise it returns only the last generated tick.
  * **delimiter_name** (*str*) – The name of the delimiter field to add.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Insert tick at the end of the stream:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> data = data.insert_at_end()
>>> otp.run(data)
                     Time  A  AT_END
0 2003-12-01 00:00:00.000  1       0
1 2003-12-01 00:00:00.001  2       0
2 2003-12-01 00:00:00.002  3       0
3 2003-12-04 00:00:00.000  0       1
```

The name of the added field can be changed:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> data = data.insert_at_end(delimiter_name='LAST_TICK')
>>> otp.run(data)
                     Time  A  LAST_TICK
0 2003-12-01 00:00:00.000  1          0
1 2003-12-01 00:00:00.001  2          0
2 2003-12-01 00:00:00.002  3          0
3 2003-12-04 00:00:00.000  0          1
```

If parameter `propagate_ticks` is set to False, then only the last tick is returned:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> data = data.insert_at_end(propagate_ticks=False)
>>> otp.run(data)
        Time  A  AT_END
0 2003-12-04  0       1
```

##### SEE ALSO
**INSERT_AT_END** OneTick event processor
