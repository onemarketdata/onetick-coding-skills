# otp.PointInTime

### ``PointInTime(source, times, offsets, offset_type='time_msec', db=utils.adaptive_to_default, tick_type=utils.adaptive, symbol=utils.adaptive_to_default, start=utils.adaptive, end=utils.adaptive, query_parameters=None)``

This function propagates ticks from `source` that are offset by
the specified number of milliseconds or by the specified number of ticks
relative to the timestamps specified in `times`.

Output tick may be generated for each specified timestamp and offset pair.

If `source` doesn’t have a tick with specified timestamp and offset, then output tick is not generated.

Fields **TICK_TIME** and **OFFSET** are also added to the output ticks,
specifying original timestamp of the tick and the offset that was specified for it.

* **Parameters:**
  * **source** (*Source*) – The source from which the data will be queried.
  * **times** (*list* **[*str* *|* *datetime* *]*) – List of timestamps to get query ticks from.
  * **offsets** (*list* **[*int* *]*) – List of integers specifying offsets for each timestamp.
  * **offset_type** ( *'time_msec'* *or*  *'num_ticks'*) – The type of offset: number of milliseconds or the number of ticks.
  * **db** (*str*) – This parameter is used to set database part of a tick type of the OneTick graph node.
    Should not be specified in most cases,
    default value will be deduced depending on configuration and query structure.
  * **tick_type** (*str*) – This parameter is used to set tick type part of a tick type of the OneTick graph node.
    Should not be specified in most cases,
    default value will be deduced depending on configuration and query structure.
  * **symbol** (*str*) – This parameter is used to set bound symbol of the OneTick graph node.
    Should not be specified in most cases,
    default value will be deduced depending on configuration and query structure.
  * **start** – Can be used to specify custom start time for this source.
    By default start time of the query will be inherited when running the query.
  * **end** – Can be used to specify custom start time for this source.
    By default end time of the query will be inherited when running the query.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
* **Return type:**
  *Source*

#### NOTE
In order for this method to have reasonable performance,
the set of queried timestamps has to be relatively small.

In other words, the points in time, which the user is interested in,
have to be quite few in order usage of this method to be justified.

##### Examples

Quotes for testing:

```python
qte = otp.Ticks(ASK_PRICE=[20, 21, 22, 23, 24, 25], BID_PRICE=[20, 21, 22, 23, 24, 25])
print(otp.run(qte))
```

```
                     Time  ASK_PRICE  BID_PRICE
0 2003-12-01 00:00:00.000         20         20
1 2003-12-01 00:00:00.001         21         21
2 2003-12-01 00:00:00.002         22         22
3 2003-12-01 00:00:00.003         23         23
4 2003-12-01 00:00:00.004         24         24
5 2003-12-01 00:00:00.005         25         25
```

Getting quotes exactly at specified timestamps:

```python
data = otp.PointInTime(qte,
                       times=[otp.dt(2003, 12, 1, 0, 0, 0, 1000), '20031201000000.003'],
                       offsets=[0])
print(otp.run(data))
```

```
                     Time  ASK_PRICE  BID_PRICE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.001         21         21 2003-12-01 00:00:00.001       0
1 2003-12-01 00:00:00.003         23         23 2003-12-01 00:00:00.003       0
```

Offset may be positive or negative.
If several offsets are specified, several output ticks may be generated for a single timestamp:

```python
data = otp.PointInTime(qte,
                       times=[otp.dt(2003, 12, 1, 0, 0, 0, 3000)],
                       offsets=[0, 1])
print(otp.run(data))
```

```
                     Time  ASK_PRICE  BID_PRICE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.003         23         23 2003-12-01 00:00:00.003       0
1 2003-12-01 00:00:00.003         24         24 2003-12-01 00:00:00.004       1
```

By default the number of milliseconds is used as an offset.
You can also specify the number of ticks as an offset:

```python
data = otp.PointInTime(qte,
                       times=[otp.dt(2003, 12, 1, 0, 0, 0, 3000)],
                       offsets=[-1, 1],
                       offset_type='num_ticks')
print(otp.run(data))
```

```
                     Time  ASK_PRICE  BID_PRICE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.003         22         22 2003-12-01 00:00:00.002      -1
1 2003-12-01 00:00:00.003         24         24 2003-12-01 00:00:00.004       1
```

##### SEE ALSO
**POINT_IN_TIME** OneTick event processor

``onetick.py.Source.point_in_time()``

``onetick.py.join_by_time()``

