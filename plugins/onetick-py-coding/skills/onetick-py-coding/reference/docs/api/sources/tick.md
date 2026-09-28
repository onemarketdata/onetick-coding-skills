# otp.Tick

### ``class Tick(data, offset, offset_part, time, timezone_for_time, symbol, db, start, end, date, tick_type, bucket_interval, bucket_units, num_ticks_per_timestamp, query_parameters, kwargs, bucket_time=None)``

Bases: ``Source``

Generates a single tick for each bucket.
By default a single tick for the whole query time interval is generated.

* **Parameters:**
  * **data** (*dict*) – Dictionary of columns names with their values.
    If specified, then parameter `kwargs` can’t be used.
  * **offset** (int, `datetime offset`,                ``otp.timedelta``) – Tick timestamp offset from query start time in offset_part.
    Default is 0 (tick timestamp will be the same as query start time).
  * **offset_part** (*one* *of*  *[**nanosecond* *,* *millisecond* *,* *second* *,* *minute* *,* *hour* *,*                             *day* *,* *dayofyear* *,* *weekday* *,* *week* *,* *month* *,* *quarter* *,* *year* *]*) – Unit of time to calculate `offset` from.
    Could be omitted if `datetime offset` or
    ``otp.timedelta`` objects are set as `offset`,
    otherwise by default it is set to ‘millisecond’.
  * **time** (``otp.datetime``) – Fixed timestamp to set to all ticks.
    Note that this time should be inside time interval set by `start` and `end` parameters
    or by query time range.
  * **timezone_for_time** (*str*) – Timezone of the `time`.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **db** (*str*) – Database name set in the OneTick graph node, which defines the server which processes the query.
    By default the symbol set when running the query will be used.
  * **start** (``otp.datetime``) – start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – end time for tick generation. By default the end time of the query will be used.
  * **date** (``otp.datetime`` - allows to specify a whole day) – instead of passing explicitly `start` and `end` parameters. If it is set along with
    the these parameters then they are ignored.
  * **tick_type** (*str*) – By default, the tick type value is not significant, and a placeholder string constant will be utilized.
    If you prefer to use the sink node’s tick type instead of specifying your own,
    you can set the value to None.
  * **bucket_interval** (int or `datetime offset objects`) – 

    Determines the length of each bucket (units depends on `bucket_units`)
    for which the tick will be generated.

    Bucket interval can also be set via `datetime offset objects`
    like ``otp.Second``, ``otp.Minute``,
    ``otp.Hour``, ``otp.Day``,
    ``otp.Month``.
    In this case you could omit setting `bucket_units` parameter.
  * **bucket_units** ( *'seconds'* *,*  *'days'* *or*  *'months'*) – Unit for value in `bucket_interval`.
    Default is ‘seconds’.
  * **num_ticks_per_timestamp** (*int*) – The number of ticks to generate for every value of timestamp.
    Default is 1.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** – Dictionary of column names with their values.
    If specified, then parameter `data` can’t be used.
  * **bucket_time** (*Literal* *[* *'start'* *,*  *'end'* *]* *,* *default=end*) – 

    Control output timestamp.
    * **start**

      the timestamp  assigned to the bucket is the start time of the bucket.
    * **end**

      the timestamp assigned to the bucket is the end time of the bucket.

##### Examples

Simple usage, generate single tick:

```
>>> t = otp.Tick(A=1, B='string', C=3.14, D=otp.dt(2000, 1, 1, 1, 1, 1, 1))
>>> otp.run(t)
        Time  A       B     C                          D
0 2003-12-01  1  string  3.14 2000-01-01 01:01:01.000001
```

Generate single tick with offset:

```
>>> t = otp.Tick(A=1, offset=otp.Minute(10))
>>> otp.run(t)
        Time           A
0 2003-12-01 00:10:00  1
```

Generate one tick for each day in a week:

```
>>> t = otp.Tick(A=1, start=otp.dt(2023, 1, 1), end=otp.dt(2023, 1, 8), bucket_interval=24 * 60 * 60)
>>> otp.run(t)
        Time  A
0 2023-01-01  1
1 2023-01-02  1
2 2023-01-03  1
3 2023-01-04  1
4 2023-01-05  1
5 2023-01-06  1
6 2023-01-07  1
```

Generate tick every hour and add 1 minute offset to ticks’ timestamps:

```
>>> t = otp.Tick(A=1, offset=1, offset_part='minute', bucket_interval=60 * 60)
>>> t.head(5)
                  Time  A
0  2003-12-01 00:01:00  1
1  2003-12-01 01:01:00  1
2  2003-12-01 02:01:00  1
3  2003-12-01 03:01:00  1
4  2003-12-01 04:01:00  1
```

Generate tick every hour and set fixed time:

```
>>> t = otp.Tick(A=1, time=otp.dt(2023, 1, 2, 3, 4, 5, 6), bucket_interval=60 * 60,
...              start=otp.dt(2023, 1, 1), end=otp.dt(2023, 1, 8))
>>> t.head(5)
                        Time  A
0 2023-01-02 03:04:05.000006  1
1 2023-01-02 03:04:05.000006  1
2 2023-01-02 03:04:05.000006  1
3 2023-01-02 03:04:05.000006  1
4 2023-01-02 03:04:05.000006  1
```

Use `datetime offset object` as a `bucket_interval`:

```python
t = otp.Tick(A=1, bucket_interval=otp.Day(1))
df = otp.run(t, start=otp.dt(2023, 1, 1), end=otp.dt(2023, 1, 5))
print(df)
```

```
        Time  A
0 2023-01-01  1
1 2023-01-02  1
2 2023-01-03  1
3 2023-01-04  1
```

##### SEE ALSO
**TICK_GENERATOR** OneTick event processor

``otp.Ticks``

