# otp.Source.point_in_time

#### ``Source.point_in_time(source, offsets, offset_type='time_msec', input_ts_fields_to_propagate=None, symbol_date=None)``

This method joins ticks from current source with the ticks from another `source`.

Joined ticks are those that are offset by
the specified number of milliseconds or by the specified number of ticks
relative to the current source’s tick timestamp.

Output tick may be generated for each specified offset, so this method may output several ticks for each input tick.

If another `source` doesn’t have a tick with specified offset, then output tick is not generated.

Fields **TICK_TIME** and **OFFSET** are also added to the output ticks,
specifying original timestamp of the joined tick and the offset that it was specified to join by.

* **Parameters:**
  * **source** (``Source`` or str) – The source from which the data will be joined or the string with the path to the .otq file
    (note that in the latter case schema can not be updated automatically with the fields from the joined query).
  * **offsets** (*list* **[*int* *]*) – List of integers specifying offsets for each timestamp.
  * **offset_type** ( *'time_msec'* *or*  *'num_ticks'*) – The type of offset: number of milliseconds or the number of ticks.
  * **input_ts_fields_to_propagate** (*list* **[*str* *]*  *|* *None*) – The list of fields to propagate from the current source.
    By default no fields (except **TIMESTAMP**) are propagated.
  * **symbol_date** (``otp.datetime``) – Symbol date that will be set for the `source` inner query.
  * **self** (*Source*)
* **Return type:**
  `Source`

#### NOTE
In order for this method to have reasonable performance,
the set of input ticks’ timestamps has to be relatively small.

In other words, the points in time, which the user is interested in,
have to be quite few in order usage of this method to be justified.

##### Examples

Quotes and trades for testing:

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

```python
trd = otp.Ticks(PRICE=[1, 3, 5], SIZE=[100, 300, 500], offset=[1, 3, 5])
print(otp.run(trd))
```

```
                     Time  PRICE  SIZE
0 2003-12-01 00:00:00.001      1   100
1 2003-12-01 00:00:00.003      3   300
2 2003-12-01 00:00:00.005      5   500
```

Joining each quote with first trade with equal or less timestamp:

```python
data = qte.point_in_time(trd, offsets=[0])
print(otp.run(data))
```

```
                     Time  PRICE  SIZE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.001      1   100 2003-12-01 00:00:00.001       0
1 2003-12-01 00:00:00.002      1   100 2003-12-01 00:00:00.001       0
2 2003-12-01 00:00:00.003      3   300 2003-12-01 00:00:00.003       0
3 2003-12-01 00:00:00.004      3   300 2003-12-01 00:00:00.003       0
4 2003-12-01 00:00:00.005      5   500 2003-12-01 00:00:00.005       0
```

By default fields from the current source are not propagated,
use parameter `input_ts_fields_to_propagate` to add them to the output:

```python
data = qte.point_in_time(trd, offsets=[0], input_ts_fields_to_propagate=['ASK_PRICE', 'BID_PRICE'])
print(otp.run(data))
```

```
                     Time  ASK_PRICE  BID_PRICE  PRICE  SIZE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.001         21         21      1   100 2003-12-01 00:00:00.001       0
1 2003-12-01 00:00:00.002         22         22      1   100 2003-12-01 00:00:00.001       0
2 2003-12-01 00:00:00.003         23         23      3   300 2003-12-01 00:00:00.003       0
3 2003-12-01 00:00:00.004         24         24      3   300 2003-12-01 00:00:00.003       0
4 2003-12-01 00:00:00.005         25         25      5   500 2003-12-01 00:00:00.005       0
```

Note that first quote was not propagated, because it doesn’t have corresponding trade.

Offset may be positive or negative.
If several offsets are specified, several output ticks may be generated for a single input tick:

```python
data = qte.point_in_time(trd, offsets=[0, 1], input_ts_fields_to_propagate=['ASK_PRICE', 'BID_PRICE'])
print(otp.run(data))
```

```
                      Time  ASK_PRICE  BID_PRICE  PRICE  SIZE               TICK_TIME  OFFSET
0  2003-12-01 00:00:00.000         20         20      1   100 2003-12-01 00:00:00.001       1
1  2003-12-01 00:00:00.001         21         21      1   100 2003-12-01 00:00:00.001       0
2  2003-12-01 00:00:00.001         21         21      1   100 2003-12-01 00:00:00.001       1
3  2003-12-01 00:00:00.002         22         22      1   100 2003-12-01 00:00:00.001       0
4  2003-12-01 00:00:00.002         22         22      3   300 2003-12-01 00:00:00.003       1
5  2003-12-01 00:00:00.003         23         23      3   300 2003-12-01 00:00:00.003       0
6  2003-12-01 00:00:00.003         23         23      3   300 2003-12-01 00:00:00.003       1
7  2003-12-01 00:00:00.004         24         24      3   300 2003-12-01 00:00:00.003       0
8  2003-12-01 00:00:00.004         24         24      5   500 2003-12-01 00:00:00.005       1
9  2003-12-01 00:00:00.005         25         25      5   500 2003-12-01 00:00:00.005       0
10 2003-12-01 00:00:00.005         25         25      5   500 2003-12-01 00:00:00.005       1
```

By default the number of milliseconds is used as an offset.
You can also specify the number of ticks as an offset:

```python
data = qte.point_in_time(trd, offset_type='num_ticks', offsets=[-1, 1],
                         input_ts_fields_to_propagate=['ASK_PRICE', 'BID_PRICE'])
print(otp.run(data))
```

```
                     Time  ASK_PRICE  BID_PRICE  PRICE  SIZE               TICK_TIME  OFFSET
0 2003-12-01 00:00:00.000         20         20      1   100 2003-12-01 00:00:00.001       1
1 2003-12-01 00:00:00.001         21         21      3   300 2003-12-01 00:00:00.003       1
2 2003-12-01 00:00:00.002         22         22      3   300 2003-12-01 00:00:00.003       1
3 2003-12-01 00:00:00.003         23         23      1   100 2003-12-01 00:00:00.001      -1
4 2003-12-01 00:00:00.003         23         23      5   500 2003-12-01 00:00:00.005       1
5 2003-12-01 00:00:00.004         24         24      1   100 2003-12-01 00:00:00.001      -1
6 2003-12-01 00:00:00.004         24         24      5   500 2003-12-01 00:00:00.005       1
7 2003-12-01 00:00:00.005         25         25      3   300 2003-12-01 00:00:00.003      -1
```

##### SEE ALSO
**POINT_IN_TIME** OneTick event processor

``onetick.py.PointInTime()``

``onetick.py.join_by_time()``

