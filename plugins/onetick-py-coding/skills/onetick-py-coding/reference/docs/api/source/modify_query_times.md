# otp.Source.modify_query_times

#### ``Source.modify_query_times(start=None, end=None, output_timestamp=None, propagate_heartbeats=True, inplace=False)``

Modify `start` and `end` time of the query.

* query times are changed for all operations
  : only **before** this method up to the source of the graph.
* all ticks’ timestamps produced by this method
  : **must** fall into original time range of the query.

It is possible to change ticks’ timestamps with parameter `output_timestamp`,
so they will stay inside the original time range.

* **Parameters:**
  * **start** (``otp.datetime`` or             ``MetaFields`` or ``Operation``) – Expression to replace query start time.
    By default, start time is not changed.
    Note that expression in this parameter can’t depend on ticks, thus only
    ``MetaFields`` and constants can be used.
  * **end** (``otp.datetime`` or             ``MetaFields`` or ``Operation``) – Expression to replace query end time.
    By default, end time is not changed.
    Note that expression in this parameter can’t depend on ticks, thus only
    ``MetaFields`` and constants can be used.
  * **output_timestamp** (``onetick.py.Operation``) – Expression that produces timestamp for each tick.
    By default, the following expression is used: `orig_start + orig_timestamp - start`
    This expression covers cases when start time of the query is changed and keeps
    timestamp inside original time range.
    Note that it doesn’t cover cases, for example, if end time was increased,
    you have to handle such cases yourself.
  * **propagate_heartbeats** (*bool*) – Controls heartbeat propagation.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

#### NOTE
Due to how OneTick works internally, tick generators
``otp.Tick`` and ``otp.Ticks``
are not affected by this method.

##### Examples

```
>>> start = otp.dt(2024, 2, 1, 4) + otp.Milli(9)
>>> end = otp.dt(2024, 2, 1, 4) + otp.Milli(12)
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data = data[['PRICE', 'SIZE']]
```

By default, method does nothing:

```
>>> t = data.modify_query_times()
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.010381671  185.49     1
1 2024-02-01 04:00:00.011224206  185.50     2
2 2024-02-01 04:00:00.011671193  185.50     1
```

See how `_START_TIME` and `_END_TIME` meta fields are changed.
They are changed *before* `modify_query_times`:

```
>>> t = data.copy()
>>> t['S_BEFORE'] = t['_START_TIME']
>>> t['E_BEFORE'] = t['_END_TIME']
>>> t = t.modify_query_times(start=t['_START_TIME'] + otp.Milli(1),
...                          end=t['_END_TIME'] - otp.Milli(1))
>>> t['S_AFTER'] = t['_START_TIME']
>>> t['E_AFTER'] = t['_END_TIME']
>>> df = otp.run(t, start=start, end=end)
>>> df[['Time', 'PRICE', 'SIZE', 'S_BEFORE', 'E_BEFORE']]
                           Time   PRICE  SIZE                S_BEFORE                E_BEFORE
0 2024-02-01 04:00:00.009381671  185.49     1 2024-02-01 04:00:00.010 2024-02-01 04:00:00.011
>>> df[['Time', 'PRICE', 'SIZE', 'S_AFTER', 'E_AFTER']]
                           Time   PRICE  SIZE                 S_AFTER                 E_AFTER
0 2024-02-01 04:00:00.009381671  185.49     1 2024-02-01 04:00:00.009 2024-02-01 04:00:00.012
```

You can decrease time interval without problems:

```
>>> t = data.modify_query_times(start=data['_START_TIME'] + otp.Milli(1),
...                             end=data['_END_TIME'] - otp.Milli(1))
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.009381671  185.49     1
```

Note that the timestamp of the tick was changed with default expression.
In this case we can output original timestamps,
because they fall into original time range:

```
>>> t = data.modify_query_times(start=data['_START_TIME'] + otp.Milli(1),
...                             end=data['_END_TIME'] - otp.Milli(1),
...                             output_timestamp=data['TIMESTAMP'])
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.010381671  185.49     1
```

But it will not work if new time range is wider than original:

```
>>> t = data.modify_query_times(start=data['_START_TIME'] - otp.Milli(1),
...                             output_timestamp=data['TIMESTAMP'])
>>> otp.run(t, start=start, end=end)  
Traceback (most recent call last):
Exception...timestamp is falling out of initial start/end time range...
```

In this case other `output_timestamp` expression must be specified:

```
>>> t = data.modify_query_times(
...     start=data['_START_TIME'] - otp.Milli(1),
...     output_timestamp=otp.math.max(data['TIMESTAMP'], data['_START_TIME'])
... )
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.009000000  186.50     6
1 2024-02-01 04:00:00.009000000  185.59     1
2 2024-02-01 04:00:00.009000000  185.49   107
3 2024-02-01 04:00:00.010381671  185.49     1
4 2024-02-01 04:00:00.011224206  185.50     2
5 2024-02-01 04:00:00.011671193  185.50     1
```

Remember that `start` and `end` parameters can’t depend on ticks:

```
>>> t = data.copy()
>>> t['X'] = 12345
>>> t = t.modify_query_times(start=t['_START_TIME'] + t['X'] - t['X'],
...                          end=t['_END_TIME'] - otp.Milli(1))
>>> otp.run(t, start=start, end=end) 
Traceback (most recent call last):
Exception...parameter must not depend on ticks...
```

Constant datetime values can be used as parameters too:

```
>>> t = data.modify_query_times(start=start + otp.Milli(1),
...                             end=end - otp.Milli(1))
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.009381671  185.49     1
```

Note that some graph patterns are not allowed when using this method.
For example, modifying query times for a branch that will be merged later:

```
>>> t1, t2 = data[data['PRICE'] > 1.3]
>>> t2 = t2.modify_query_times(start=start + otp.Milli(1))
>>> t = otp.merge([t1, t2])
>>> otp.run(t, start=start, end=end) 
Traceback (most recent call last):
Exception...Invalid graph...time bound to a node...an intermediate node in one of the cycles in graph...
```

##### SEE ALSO
**MODIFY_QUERY_TIMES** OneTick event processor

``onetick.py.Source.time_interval_shift()``

