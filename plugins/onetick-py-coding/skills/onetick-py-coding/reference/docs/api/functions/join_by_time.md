# otp.join_by_time

### ``join_by_time(sources, how='outer', on=None, policy=None, check_schema=True, leading=0, match_if_identical_times=None, output_type_index=None, use_rename_ep=True, source_fields_order=None, symbols=None)``

Joins ticks from multiple input time series, based on input tick timestamps.

`leading` source tick joined with already arrived ticks from other sources.

```
>>> leading = otp.Ticks(A=[1, 2], offset=[1, 3])
>>> other = otp.Ticks(B=[1], offset=[2])
>>> otp.run(otp.join_by_time([leading, other]))
                     Time  A  B
0 2003-12-01 00:00:00.001  1  0
1 2003-12-01 00:00:00.003  2  1
```

* **Parameters:**
  * **sources** (Collection`[`Source``]) – The collection of Source objects which will be joined
  * **how** ( *'outer'* *or*  *'inner'*) – The method of join (“inner” or “outer”).
    Inner join logic will propagate ticks only if all sources participated in forming it.
    Outer join will propagate all ticks even if they couldn’t be joined with other sources
    (in this case the fields from other sources will have “zero” values depending on the type of the field).
    Default is “outer”.
  * **on** (Collection`[`Column``]) – 

    `on` add an extra check to join - only ticks with same `on` fields will be joined
    ```
    >>> leading = otp.Ticks(A=[1, 2], offset=[1, 3])
    >>> other = otp.Ticks(A=[2, 2], B=[1, 2], offset=[0, 2])
    >>> otp.run(otp.join_by_time([leading, other], on=['A']))
                         Time  A  B
    0 2003-12-01 00:00:00.001  1  0
    1 2003-12-01 00:00:00.003  2  2
    ```
  * **policy** ( *'arrival_order'* *,*  *'latest_ticks'* *,*  *'each_for_leader_with_first'* *or*  *'each_for_leader_with_latest'*) – 

    Policy of joining ticks with the same timestamps.
    The default value is “arrival_order” by default, but is set to “latest_ticks”
    if parameter `match_if_identical_times` is set to True.
    ```
    >>> leading = otp.Ticks(A=[1, 2], offset=[0, 0], OMDSEQ=[0, 3])
    >>> other = otp.Ticks(B=[1, 2], offset=[0, 0], OMDSEQ=[2, 4])
    ```

    Note: in the examples below we assume that all ticks have same timestamps, but order of ticks as in example.
    OMDSEQ is a special field that store order of ticks with same timestamp
    - `arrival_order`
      output tick generated on arrival of `leading` source tick

    ```
    >>> data = otp.join_by_time([leading, other], policy='arrival_order')
    >>> otp.run(data)[['Time', 'A', 'B']]
            Time  A  B
    0 2003-12-01  1  0
    1 2003-12-01  2  1
    ```

    - `latest_ticks`
      Tick generated at the time of expiration of a particular timestamp (when all ticks from all sources
      for current timestamp arrived). Only latest tick from `leading` source will be used.

    ```
    >>> data = otp.join_by_time([leading, other], policy='latest_ticks')
    >>> otp.run(data)[['Time', 'A', 'B']]
            Time  A  B
    0 2003-12-01  2  2
    ```

    - `each_for_leader_with_first`
      Each tick from `leading` source will be joined with first tick from other sources for current timestamp

    ```
    >>> data = otp.join_by_time(
    ...     [leading, other],
    ...     policy='each_for_leader_with_first'
    ... )
    >>> otp.run(data)[['Time', 'A', 'B']]
            Time  A  B
    0 2003-12-01  1  1
    1 2003-12-01  2  1
    ```

    - `each_for_leader_with_latest`
      Each tick from `leading` source will be joined with last tick from other sources for current timestamp

    ```
    >>> data = otp.join_by_time(
    ...     [leading, other],
    ...     policy='each_for_leader_with_latest'
    ... )
    >>> otp.run(data)[['Time', 'A', 'B']]
            Time  A  B
    0 2003-12-01  1  2
    1 2003-12-01  2  2
    ```
  * **check_schema** (*bool*) – If True onetick.py will check that all columns names are unambiguous
    and columns listed in on param are exists in sources schema.
    Which can lead to false positive error
    in case of some event processors were sink to Source. To avoid this set check_scheme to False.
  * **leading** (int, ‘all’, ``Source``, list of int, list of ``Source``) – A list of sources or their indexes. If this parameter is ‘all’, every source is considered to be leading.
    The logic of the leading source depends on `policy` parameter.
    The default value is 0, meaning the first specified source will be the leader.
  * **match_if_identical_times** (*bool*) – A True value of this parameter causes an output tick to be formed from input ticks with identical timestamps
    only.
    If parameter `how` is set to ‘outer’,
    default values of fields (`otp.nan`, 0, empty string) are propagated for
    sources that did not tick at a given timestamp.
    If this parameter is set to True, the default value of `policy` parameter is set to ‘latest_ticks’.
  * **output_type_index** (*int*) – Specifies index of source in `sources` from which type and properties of output will be taken.
    Useful when joining sources that inherited from ``Source``.
    By default output object type will be ``Source``.
  * **use_rename_ep** (*bool*) – This parameter specifies if `onetick.query.RenameFields`
    event processor will be used in internal implementation of this function or not.
    This event processor can’t be used in generic aggregations, so set this parameter to False
    if `join_by_time` is used in generic aggregation logic.
  * **source_fields_order** (list of int, list of ``Source``) – Controls the order of fields in output ticks.
    If set, all input sources indexes or objects must be specified.
    By default, the order of the sources is the same as in the `sources` list.
  * **symbols** (str, list of str or functions, ``Source``, `onetick.query.GraphQuery`) – 

    Bound symbol(s) passed as a string, a list of strings, or as a “symbols” query which results
    include the `SYMBOL_NAME` column. The start/end times for the
    symbols query will taken from the ``run()`` params.
    See `symbols` for more details.

    #### WARNING
    Passing more than one source for join and setting `symbols` parameter at the same time aren’t supported

    #### NOTE
    If bound symbols are specified as ``Source`` or `onetick.query.GraphQuery`,
    you **should** set schema for returned ``Source`` object manually:
    `onetick-py` couldn’t determine symbols from sub-query before running the query.

    #### NOTE
    If bound symbols are specified as ``Source`` or `onetick.query.GraphQuery`,
    and this sub-query returns only one symbol name, output columns wouldn’t have a prefix with symbol name.
* **Returns:**
  A time series of ticks.
* **Return type:**
  ``Source`` or same class as `sources[output_type_index]`

#### NOTE
In case different `sources` have matching columns, the exception will be raised.

To fix this error,
functions ``Source.add_prefix()`` or ``Source.add_suffix()`` can be used to rename all columns in the source.

Note that resulting **TIMESTAMP** pseudo-column will be taken from the leading source,
and timestamps of ticks from non-leading sources will not be added to the output,
so if you need to save them, you need to copy the timestamp to some other column.

See examples below.

##### Examples

```
>>> d1 = otp.Ticks({'A': [1, 2, 3], 'offset': [1, 2, 3]})
>>> d2 = otp.Ticks({'B': [1, 2, 4], 'offset': [1, 2, 4]})
>>> otp.run(d1)
                     Time  A
0 2003-12-01 00:00:00.001  1
1 2003-12-01 00:00:00.002  2
2 2003-12-01 00:00:00.003  3
>>> otp.run(d2)
                     Time  B
0 2003-12-01 00:00:00.001  1
1 2003-12-01 00:00:00.002  2
2 2003-12-01 00:00:00.004  4
```

Default joining logic, outer join with the first source is the leader by default:

```
>>> data = otp.join_by_time([d1, d2])
>>> otp.run(data)
                     Time  A  B
0 2003-12-01 00:00:00.001  1  0
1 2003-12-01 00:00:00.002  2  1
2 2003-12-01 00:00:00.003  3  2
```

Leading source can be changed by using parameter `leading`:

```
>>> data = otp.join_by_time([d1, d2], leading=1)
>>> otp.run(data)
                     Time  A  B
0 2003-12-01 00:00:00.001  1  1
1 2003-12-01 00:00:00.002  2  2
2 2003-12-01 00:00:00.004  3  4
```

Note that OneTick’s logic is different depending on the order of sources specified,
so specifying `leading` parameter in the previous example is not the same as changing the order of sources here:

```
>>> data = otp.join_by_time([d2, d1], leading=0)
>>> otp.run(data)
                     Time  B  A
0 2003-12-01 00:00:00.001  1  0
1 2003-12-01 00:00:00.002  2  1
2 2003-12-01 00:00:00.004  4  3
```

Parameter `source_fields_order` can be used to change the order of fields in the output,
but it also affects the joining logic the same way as changing the order of sources:

```
>>> data = otp.join_by_time([d1, d2], leading=1, source_fields_order=[1, 0])
>>> otp.run(data)
                     Time  B  A
0 2003-12-01 00:00:00.001  1  0
1 2003-12-01 00:00:00.002  2  1
2 2003-12-01 00:00:00.004  4  3
```

Parameter `how` can be set to “inner”.
In this case only ticks that were successfully joined from all sources will be propagated:

```
>>> data = otp.join_by_time([d1, d2], how='inner')
>>> otp.run(data)
                     Time  A  B
0 2003-12-01 00:00:00.002  2  1
1 2003-12-01 00:00:00.003  3  2
```

Set parameter `match_if_identical_times` to only join ticks with the same timestamps:

```
>>> data = otp.join_by_time([d1, d2], how='inner', match_if_identical_times=True)
>>> otp.run(data)
                     Time  A  B
0 2003-12-01 00:00:00.001  1  1
1 2003-12-01 00:00:00.002  2  2
```

In case of conflicting names in different sources, exception will be raised:

```
>>> d3 = otp.Ticks({'A': [1, 2, 4], 'offset': [1, 2, 4]})
>>> data = otp.join_by_time([d1, d3])
Traceback (most recent call last):
    ...
ValueError: There are matched columns between sources: A
```

Adding prefix to right source for all columns will fix this problem:

```
>>> data = otp.join_by_time([d1, d3.add_prefix('right_')])
>>> otp.run(data)
                     Time  A  right_A
0 2003-12-01 00:00:00.001  1        0
1 2003-12-01 00:00:00.002  2        1
2 2003-12-01 00:00:00.003  3        2
```

Note that timestamps from the non-leading source are not added to the output.
You can add them manually in a different field:

```
>>> d3['D3_TIMESTAMP'] = d3['TIMESTAMP']
>>> data = otp.join_by_time([d1, d3.add_prefix('right_')])
>>> otp.run(data)
                     Time  A  right_A      right_D3_TIMESTAMP
0 2003-12-01 00:00:00.001  1        0 1969-12-31 19:00:00.000
1 2003-12-01 00:00:00.002  2        1 2003-12-01 00:00:00.001
2 2003-12-01 00:00:00.003  3        2 2003-12-01 00:00:00.002
```

Use parameter `output_type_index` to specify which input class to use to create output object.
It may be useful in case some custom user class was used as input:

```
>>> class CustomTick(otp.Tick):
...     def custom_method(self):
...         return 'custom_result'
>>> data1 = otp.Tick(A=1)
>>> data2 = CustomTick(B=2)
>>> data = otp.join_by_time([data1, data2], match_if_identical_times=True, output_type_index=1)
>>> type(data)
<class 'onetick.py.functions.CustomTick'>
>>> data.custom_method()
'custom_result'
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  2
```

Use parameter `source_fields_order` to specify the order of output fields:

```
>>> a = otp.Ticks(A=[1, 2])
>>> b = otp.Ticks(B=[1, 2])
>>> c = otp.Ticks(C=[1, 2])
>>> data = otp.join_by_time([a, b, c], match_if_identical_times=True, source_fields_order=[c, b, a])
>>> otp.run(data)
                     Time  C  B  A
0 2003-12-01 00:00:00.000  1  1  1
1 2003-12-01 00:00:00.001  2  2  2
```

Indexes can be used too:

```
>>> data = otp.join_by_time([a, b, c], match_if_identical_times=True, source_fields_order=[1, 2, 0])
>>> otp.run(data)
                     Time  B  C  A
0 2003-12-01 00:00:00.000  1  1  1
1 2003-12-01 00:00:00.001  2  2  2
```

Use parameter symbols to specify bound symbols:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = otp.join_by_time([data], symbols=['A', 'B'], match_if_identical_times=True)
>>> otp.run(data)
                     Time  A.X  B.X
0 2003-12-01 00:00:00.000    1    1
1 2003-12-01 00:00:00.001    2    2
2 2003-12-01 00:00:00.002    3    3
3 2003-12-01 00:00:00.003    4    4
```

##### SEE ALSO
**JOIN_BY_TIME** OneTick event processor
