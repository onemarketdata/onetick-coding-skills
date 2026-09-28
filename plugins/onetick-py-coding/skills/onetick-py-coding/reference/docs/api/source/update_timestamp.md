# otp.Source.update_timestamp

#### ``Source.update_timestamp(timestamp_field, timestamp_psec_field=None, max_delay_of_original_timestamp=0, max_delay_of_new_timestamp=0, max_out_of_order_interval=0, max_delay_handling='complain', out_of_order_timestamp_handling='complain', zero_timestamp_handling=None, log_sequence_violations=False, inplace=False)``

Assigns alternative timestamps to input ticks.

In case resulting timestamps are out of order, time series is sorted in ascending order of those timestamps.

Ticks with equal alternative timestamps are sorted in ascending order of the respective original timestamps,
and equal ones among those are further sorted by ascending values of the **OMDSEQ** field,
if such a field is present.

* **Parameters:**
  * **timestamp_field** (*str*) – Specifies the name of the input field, which is assumed to carry alternative timestamps.
  * **timestamp_psec_field** (*str*) – 

    Fractional (< 1 millisecond) parts of alternative timestamps will be taken from this field.
    They are assumed to be in picoseconds (0.001 of nanosecond).

    If this field is specified, then even if alternative timestamps have nanosecond granularity,
    only millisecond parts of them will be taken, nanosecond part will be rewritten.

    Useful in case `timestamp_field` has lower than millisecond granularity.
  * **max_delay_of_original_timestamp** (int or `datetime offset`) – 

    Changes the start time of the query to `original_start_time - max_delay_of_original_timestamp`
    to make sure it processes all possible ticks that might have a new timestamp in the query time range.

    If integer value is specified, it is assumed to be milliseconds.
  * **max_delay_of_new_timestamp** (int or `datetime offset`) – 

    Changes the end time of the query to `original_end_time + max_delay_of_new_timestamp`
    to make sure it processes all possible ticks that might have a new timestamp in the query time range.

    If integer value is specified, it is assumed to be milliseconds.
  * **max_out_of_order_interval** (int or `datetime offset`) – 

    Specifies the maximum out-of-order interval for alternative timestamps.
    Ticks with new timestamps out of order will be sorted in ascending order.

    This is the only parameter that leads to accumulation of ticks.

    If integer value is specified, it is assumed to be milliseconds.
  * **max_delay_handling** (*str* *(* *'complain'* *or*  *'discard'* *or*  *'use_original_timestamp'* *or*  *'use_new_timestamp'* *)*) – 

    This parameter how to process ticks that are delayed even more than specified in
    `max_delay_of_original_timestamp` or `max_delay_of_new_timestamp` parameters.
    - **complain**: raise an exception
    - **discard**: do not add tick to the output time series
    - **use_original_timestamp**: assign original timestamp to the tick
    - **use_new_timestamp**: try to assign new timestamp to the tick. If the new timestamp of previous tick
      : is greater than new timestamp of this tick then previous one is used.
        **NOTE: A previously propagated timestamp could be from a heartbeat.**
        When there is no previous propagated tick and the new timestamp falls behind the query start time,
        the latter is preferred, while query end time is preferred if the new timestamp exceeds it.

    This parameter is processed *before* any action from `out_of_order_timestamp_handling` parameter.
  * **out_of_order_timestamp_handling** (*str* *(* *'complain'* *or*  *'use_previous_value'* *or*  *'use_original_timestamp'* *)*) – 

    This parameter how to process ticks that are out of order even more than specified in
    `max_out_of_order_interval` parameter.
    - **complain**: raise an exception
    - **use_previous_value**: assign new timestamp from a previous propagated tick
    - **use_original_timestamp**: try to use original timestamp. If maximum out-of-order interval is still
      : exceeded then exception will be thrown.

    This parameter is processed *after* any action from `max_delay_handling` parameter.
  * **zero_timestamp_handling** (*str* *(* *'preserve_sequence'* *)* *,* *optional*) – 

    This parameter specifies how to process ticks with zero alternative timestamps.

    If value is None, actions from `max_delay_handling`
    and `out_of_order_timestamp_handling` are performed on it.

    If value is **preserve_sequence** then new timestamp will be set to the maximum between
    query start time and the new timestamp of the previous propagated tick.
  * **log_sequence_violations** (*bool*) – If set to True, then warnings about actions from parameters `out_of_order_timestamp_handling`,
    `max_delay_handling` and `zero_timestamp_handling` are logged into the log file.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

#### NOTE
In case parameters `max_delay_of_original_timestamp` or `max_delay_of_new_timestamp` are specified
this method automatically uses ``modify_query_times()`` method, affecting
the start or end time of the query thus possibly changing some logic of the nodes placed higher in the graph.

Also there are some limitations on using this method in the graph, e.g. this method can’t be used in the
“diamond” pattern and can’t be used twice in the same graph.

##### Examples

Data and timestamps from the database:

```
>>> start = otp.dt(2024, 2, 1)
>>> end = otp.dt(2024, 2, 2)
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data = data[['PRICE', 'SIZE']]
>>> df = otp.run(data, start=start, end=end)
>>> df.head()
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.008283417  186.50     6
1 2024-02-01 04:00:00.008290927  185.59     1
2 2024-02-01 04:00:00.008291153  185.49   107
3 2024-02-01 04:00:00.010381671  185.49     1
4 2024-02-01 04:00:00.011224206  185.50     2
```

Adding one hour to all ticks.
Parameter `max_delay_of_original_timestamp` must be specified in this case:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data['ORIG_TS'] = data['TIMESTAMP']
>>> data['NEW_TS'] = data['TIMESTAMP'] + otp.Hour(1)
>>> data = data.update_timestamp('NEW_TS', max_delay_of_original_timestamp=otp.Hour(1))
>>> df = otp.run(data, start=start, end=end)
>>> df.head()[['Time', 'PRICE', 'SIZE', 'ORIG_TS']]
                           Time   PRICE  SIZE                       ORIG_TS
0 2024-02-01 05:00:00.008283417  186.50     6 2024-02-01 04:00:00.008283417
1 2024-02-01 05:00:00.008290927  185.59     1 2024-02-01 04:00:00.008290927
2 2024-02-01 05:00:00.008291153  185.49   107 2024-02-01 04:00:00.008291153
3 2024-02-01 05:00:00.010381671  185.49     1 2024-02-01 04:00:00.010381671
4 2024-02-01 05:00:00.011224206  185.50     2 2024-02-01 04:00:00.011224206
```

Subtracting one day from all ticks.
Parameter `max_delay_of_new_timestamp` must be specified in this case.

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data['ORIG_TS'] = data['TIMESTAMP']
>>> data['NEW_TS'] = data['TIMESTAMP'] - otp.Day(1)
>>> data = data.update_timestamp('NEW_TS', max_delay_of_new_timestamp=otp.Day(1))
>>> df = otp.run(data, start=start - otp.Day(1), end=end)
>>> df.head()[['Time', 'PRICE', 'SIZE', 'ORIG_TS']]
                           Time   PRICE  SIZE                       ORIG_TS
0 2024-01-31 04:00:00.008283417  186.50     6 2024-02-01 04:00:00.008283417
1 2024-01-31 04:00:00.008290927  185.59     1 2024-02-01 04:00:00.008290927
2 2024-01-31 04:00:00.008291153  185.49   107 2024-02-01 04:00:00.008291153
3 2024-01-31 04:00:00.010381671  185.49     1 2024-02-01 04:00:00.010381671
4 2024-01-31 04:00:00.011224206  185.50     2 2024-02-01 04:00:00.011224206
```

Parameter `max_delay_handling` can be used to specify how to handle ticks exceeding the maximum:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data['ORIG_TS'] = data['TIMESTAMP']
>>> data['NEW_TS'] = data.apply(
...     lambda row: row['TIMESTAMP'] + otp.Hour(24)
...     if row['SIZE'] == 1
...     else row['TIMESTAMP'] + otp.Hour(1)
... )
>>> data = data.update_timestamp('NEW_TS',
...                              max_delay_of_original_timestamp=otp.Hour(1),
...                              max_delay_handling='discard')
>>> df = otp.run(data, start=start, end=end)
>>> df.head()[['Time', 'PRICE', 'SIZE', 'ORIG_TS']]
                           Time   PRICE  SIZE                       ORIG_TS
0 2024-02-01 05:00:00.008283417  186.50     6 2024-02-01 04:00:00.008283417
1 2024-02-01 05:00:00.008291153  185.49   107 2024-02-01 04:00:00.008291153
2 2024-02-01 05:00:00.011224206  185.50     2 2024-02-01 04:00:00.011224206
3 2024-02-01 05:00:00.012555438  185.50     2 2024-02-01 04:00:00.012555438
4 2024-02-01 05:00:00.012759751  185.50    45 2024-02-01 04:00:00.012759751
```

Parameter `max_out_of_order_interval` can be used in case new timestamps are out of order:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data['ORIG_TS'] = data['TIMESTAMP']
>>> data = data.agg({'COUNT': otp.agg.count()}, running=True, all_fields=True)
>>> data['NEW_TS'] = data['TIMESTAMP'] - otp.Minute(data['COUNT'] % 60)
>>> data = data.update_timestamp('NEW_TS',
...                              max_delay_of_new_timestamp=otp.Hour(1),
...                              max_out_of_order_interval=otp.Hour(1))
>>> df = otp.run(data, start=start - otp.Hour(2), end=end)
>>> df.head()[['Time', 'PRICE', 'SIZE', 'ORIG_TS', 'COUNT']]
                           Time   PRICE  SIZE                       ORIG_TS  COUNT
0 2024-02-01 03:01:06.964168852  185.64     1 2024-02-01 04:00:06.964168852     59
1 2024-02-01 03:02:06.964163033  185.64     1 2024-02-01 04:00:06.964163033     58
2 2024-02-01 03:03:06.964159309  185.64     2 2024-02-01 04:00:06.964159309     57
3 2024-02-01 03:04:06.964159135  185.65    50 2024-02-01 04:00:06.964159135     56
4 2024-02-01 03:04:14.610956524  185.75     4 2024-02-01 04:03:14.610956524    119
```

##### SEE ALSO
**UPDATE_TIMESTAMP** OneTick event processor
