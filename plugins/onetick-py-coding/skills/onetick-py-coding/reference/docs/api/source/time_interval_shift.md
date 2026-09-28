# otp.Source.time_interval_shift

#### ``Source.time_interval_shift(shift, inplace=False)``

Shifting time interval for a source.

The whole data flow is shifted all the way up to the source of the graph.

The start and end times of the query will be changed for all operations before this method,
and will stay the same after this method.

WARNING: The ticks’ timestamps *are changed* automatically so they fit into original time range.

You will get different set of ticks from the database, but the timestamps of the ticks
from that database will not be the same as in the database.

They need to be changed so they fit into the original query time range.
See details in ``onetick.py.Source.modify_query_times()``.

* **Parameters:**
  * **shift** (int or `datetime offset`) – 

    Offset to shift the whole time interval.
    Can be positive or negative.
    Positive value moves time interval into the future, negative – to the past.
    int values are interpreted as milliseconds.

    Timestamps of the ticks will be changed so they fit into the original query time range
    by subtracting `shift` from each timestamp.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

–> Also see use-case using ``time_interval_shift()`` for calculating
`Markouts`

```
>>> start = otp.dt(2024, 2, 1, 4) + otp.Milli(9)
>>> end = otp.dt(2024, 2, 1, 4) + otp.Milli(12)
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data = data[['PRICE', 'SIZE']]
```

Default data:

```
>>> otp.run(data, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.010381671  185.49     1
1 2024-02-01 04:00:00.011224206  185.50     2
2 2024-02-01 04:00:00.011671193  185.50     1
```

Shifting time window will result in different set of ticks,
but the ticks will have their timestamps changed to fit into original time range.

```
>>> t = data.time_interval_shift(shift=-otp.Milli(1))
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.009283417  186.50     6
1 2024-02-01 04:00:00.009290927  185.59     1
2 2024-02-01 04:00:00.009291153  185.49   107
3 2024-02-01 04:00:00.011381671  185.49     1
```

```
>>> t = data.time_interval_shift(shift=otp.Milli(2))
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.009224206  185.50     2
1 2024-02-01 04:00:00.009671193  185.50     1
2 2024-02-01 04:00:00.010555438  185.50     2
3 2024-02-01 04:00:00.010759751  185.50    45
4 2024-02-01 04:00:00.010928231  185.68    11
5 2024-02-01 04:00:00.010930606  185.68    10
6 2024-02-01 04:00:00.010947024  185.68    23
7 2024-02-01 04:00:00.010987210  185.72     5
```

Note that tick generators
``otp.Tick`` and ``otp.Ticks``
are not really affected by this method, they will have the same timestamps:

```
>>> t = otp.Tick(A=1)
>>> otp.run(t)
        Time  A
0 2003-12-01  1
```

```
>>> t = t.time_interval_shift(shift=otp.Second(1))
>>> otp.run(t)
        Time  A
0 2003-12-01  1
```

##### SEE ALSO
``onetick.py.Source.modify_query_times()``

``onetick.py.Source.time_interval_change()``

