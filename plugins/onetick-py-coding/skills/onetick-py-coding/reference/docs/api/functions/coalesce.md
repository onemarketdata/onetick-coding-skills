# otp.coalesce

### ``coalesce(sources, max_source_delay=0.0, output_type_index=None)``

Used to fill the gaps in one time series with the ticks from one or several other time series.

This event processor considers ticks that arrive from several sources at the same time as being the same,
allowing for possible delay across the sources when determining whether the ticks are the same.
When the same tick arrives from several sources, it is only propagated from the source
that has the highest priority among those sources.
Input ticks do not necessarily have the same structure - they can have different fields.

In order to distinguish time series the event processor adds the SYMBOL_NAME field.
Also SOURCE field is added to each tick which lacks it to identify the source from which the tick is coming.
Hence, one must avoid adding SOURCE field in event processors positioned after COALSECE.

* **Parameters:**
  * **sources** (list of ``Source``) – List of the sources to coalesce. Also, this list is treated as priority order.
    First member of the list has the highest priority when determining whether ticks are the same.
  * **max_source_delay** (*float*) – The maximum time in seconds by which a tick from one input time series
    can arrive later than the same tick from another time series.
  * **output_type_index** (*int*) – Specifies index of source in `sources` from which type and properties of output will be taken.
    Useful when merging sources that inherited from ``Source``.
    By default, output object type will be ``Source``.
* **Returns:**
  A time series of ticks.
* **Return type:**
  ``Source``

##### Examples

If ticks from different sources have the same time,
only the tick from source with the highest priority will be propagated.

```
>>> data1 = otp.Ticks(A=[1, 2])
>>> data2 = otp.Ticks(A=[3, 4])
>>> data = otp.coalesce([data2, data1])
>>> otp.run(data)[['Time', 'A']]
                     Time A
0 2003-12-01 00:00:00.000 3
1 2003-12-01 00:00:00.001 4
```

We can use `max_source_delay` parameter to expand time interval in which
ticks are considered to have the “same time”.

```
>>> data1 = otp.Ticks({
...     'A': [1, 2, 3],
...     'offset': [0, 3000, 6000],
... })
>>> data2 = otp.Ticks({
...     'A': [4, 5, 6],
...     # 4 is delayed by less than one second from 1
...     # 5 is delayed by one second from 2
...     # 6 is delayed by more than one second from 3
...     'offset': [999, 4000, 7001],
... })
>>> data = otp.coalesce([data2, data1], max_source_delay=1)
>>> otp.run(data)[['Time', 'A']]
                     Time  A
0 2003-12-01 00:00:00.999  4
1 2003-12-01 00:00:04.000  5
2 2003-12-01 00:00:06.000  3
3 2003-12-01 00:00:07.001  6
```

##### SEE ALSO
**COALESCE** OneTick event processor
