# otp.Ticks

### ``class Ticks(data=None, symbol=utils.adaptive_to_default, db=utils.adaptive_to_default, start=utils.adaptive, end=utils.adaptive, tick_type=utils.adaptive, timezone_for_time=None, offset=utils.adaptive, query_parameters=None, **inplace_data)``

Bases:

Data source that generates ticks.

By default ticks are placed with the 1 millisecond offset from
each other starting from the start of the query interval.

The offset for each tick can be changed using the
special reserved field name `offset`, that specifies the time offset from the query start time.
`offset` can be an integer, `datetime offset` object
or ``otp.timedelta``.

* **Parameters:**
  * **data** (dict, list or `pandas.DataFrame`, optional) – 

    Ticks values
    * `dict` – <field_name>: <values>
    * `list` – [[<field_names>], [<first_tick_values>], …, [<n_tick_values>]]
    * `DataFrame`

    #### Deprecated
    Deprecated since version 1.178.0.

    * `None` – `inplace_data` will be used
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **db** (*str*) – Database to use for tick generation
  * **start** (``datetime.datetime``, ``otp.datetime``,                     ``onetick.py.adaptive``) – Timestamp for data generation
  * **end** (``datetime.datetime``, ``otp.datetime``,                     ``onetick.py.adaptive``) – Timestamp for data generation
  * **tick_type** (*str*) – tick type for data generation
  * **timezone_for_time** (*str*) – timezone for data generation
  * **offset** (int, `datetime offset` or ``otp.timedelta``             or list of such values or None) – Specifies the time offset for each tick from the query start time.
    Should be specified as the list of values, one for each tick,
    or as a single value that will be the same for all ticks.
    Special value None will disable changing timestamps for each tick,
    so all timestamps will be set to the query start time.
    Can’t be used at the same time with the column offset.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **\*\*inplace_data** (*list*) – <field_name>: list(<field_values>)

##### Examples

Pass the data as a dictionary:

```
>>> d = otp.Ticks({'A': [1, 2, 3], 'B': [4, 5, 6]})
>>> otp.run(d)
                     Time  A  B
0 2003-12-01 00:00:00.000  1  4
1 2003-12-01 00:00:00.001  2  5
2 2003-12-01 00:00:00.002  3  6
```

Pass the data using key-value arguments:

```
>>> d = otp.Ticks(A=[1, 2, 3], B=[4, 5, 6])
>>> otp.run(d)
                     Time  A  B
0 2003-12-01 00:00:00.000  1  4
1 2003-12-01 00:00:00.001  2  5
2 2003-12-01 00:00:00.002  3  6
```

Pass the data using list:

```
>>> d = otp.Ticks([['A', 'B'],
...                [1, 4],
...                [2, 5],
...                [3, 6]])
>>> otp.run(d)
                     Time  A  B
0 2003-12-01 00:00:00.000  1  4
1 2003-12-01 00:00:00.001  2  5
2 2003-12-01 00:00:00.002  3  6
```

Pass the data using `pandas.DataFrame`.
DataFrame should have a `Time` column containing datetime objects.

```
>>> start_datetime = datetime.datetime(2023, 1, 1, 12)
>>> time_array = [start_datetime + otp.Hour(1) + otp.Nano(1)]
>>> a_array = [start_datetime - otp.Day(15) - otp.Nano(7)]
>>> df = pd.DataFrame({'Time': time_array,'A': a_array})
>>> data = otp.Ticks(df)  
>>> otp.run(data, start=start_datetime, end=start_datetime + otp.Day(1))  
                           Time                             A
0 2023-01-01 13:00:00.000000001 2022-12-17 11:59:59.999999993
```

Example with setting `offset` for each tick:

```
>>> data = otp.Ticks(X=[1, 2, 3], offset=[0, otp.Nano(1), 1])
>>> otp.run(data)
                           Time  X
0 2003-12-01 00:00:00.000000000  1
1 2003-12-01 00:00:00.000000001  2
2 2003-12-01 00:00:00.001000000  3
```

Remove the `offset` for all ticks, in this case the timestamp of each tick is set to the start time of the query:

```
>>> data = otp.Ticks(X=[1, 2, 3], offset=None)
>>> otp.run(data)
        Time  X
0 2003-12-01  1
1 2003-12-01  2
2 2003-12-01  3
```

Parameter `offset` allows to set the same value for all ticks:

```
>>> data = otp.Ticks(X=[1, 2, 3], offset=otp.Nano(13))
>>> otp.run(data)
                           Time  X
0 2003-12-01 00:00:00.000000013  1
1 2003-12-01 00:00:00.000000013  2
2 2003-12-01 00:00:00.000000013  3
```

##### SEE ALSO
**TICK_GENERATOR** OneTick event processor

**CSV_FILE_LISTING** OneTick event processor

``otp.Tick``

