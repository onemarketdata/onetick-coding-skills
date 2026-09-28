# otp.Source.estimate_ts_delay

#### ``Source.estimate_ts_delay(other, input_field1_name, input_field2_name, smallest_time_granularity_msec=1, max_ts_delay_msec=1000, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new', group_by=None, groups_to_display='all')``

Given two time series of ticks, computes how much delay the second series has in relation to the first.
A negative delay should be interpreted as the first series being delayed instead.

The two series do not necessarily have to be identical with respect to the delay,
nor do they have to represent the same quantity (i.e. be of the same magnitude).
The only requirement to get meaningful results is that the two series be (linearly) correlated.

Output ticks always have 2 fields:

> * *DELAY_MS*, which is the computed delay in milliseconds, and
> * *CORRELATION*, which is the Zero-Normalized Cross-Correlation of the two time series
>   after that delay is applied.
* **Parameters:**
  * **other** (*Source*) – The other source.
  * **input_field1_name** (*str*) – The name of the compared field from the first source.
  * **input_field2_name** (*str*) – The name of the compared field from the second source.
  * **smallest_time_granularity_msec** (*int* *,* *default=1*) – This method works by first sampling the source tick series with a constant rate.
    This is the sampling interval (1 / rate).
    As a consequence, any computed delay will be divisible by this value.
    It is important to carefully choose this parameter, as this method has a computational cost of O(N \* log(N))
    per bucket, where N = (*duration_of_bucket_in_msec* + `max_ts_delay_msec`) / `smallest_time_granularity_msec`.
    Default: 1.
  * **max_ts_delay_msec** (*int* *,* *default=1000*) – The known upper bound on the delay’s magnitude.
    The computed delay will never be greater than this value.
    Default: 1000.
  * **bucket_interval** (int or float or ``Operation`` or ``OnetickParameter`` or ``symbol parameter`` or `datetime offset object`, default=0) – 

    Determines the length of each bucket (units depends on `bucket_units`).

    If ``Operation`` of bool type is passed, acts as `bucket_end_condition`.

    Bucket interval can also be set as a *float* value
    if `bucket_units` is set to *seconds*.
    Note that values less than 0.001 (1 millisecond) are not supported.

    Bucket interval can be set via some of the `datetime offset objects`:
    ``otp.Milli``, ``otp.Second``,
    ``otp.Minute``, ``otp.Hour``,
    ``otp.Day``, ``otp.Month``.
    In this case you could omit setting `bucket_units` parameter.

    Bucket interval can also be set with integer ``OnetickParameter``
    or ``symbol parameter``.
  * **bucket_time** (*Literal* *[* *'start'* *,*  *'end'* *]* *,* *default=end*) – 

    Control output timestamp.
    * **start**

      the timestamp  assigned to the bucket is the start time of the bucket.
    * **end**

      the timestamp assigned to the bucket is the end time of the bucket.
  * **bucket_units** (*Optional* *[**Literal* *[* *'seconds'* *,*  *'ticks'* *,*  *'days'* *,*  *'months'* *,*  *'flexible'* *]* *]* *,* *default=None*) – 

    Set bucket interval units.

    By default, if `bucket_units` and `bucket_end_condition` not specified, set to **seconds**.
    If `bucket_end_condition` specified, then `bucket_units` set to **flexible**.

    If set to **flexible** then `bucket_end_condition` must be set.

    Note that **seconds** bucket unit doesn’t take into account daylight-saving time of the timezone,
    so you may not get expected results when using, for example, 24 \* 60 \* 60 seconds as bucket interval.
    In such case use **days** bucket unit instead.
    See example in ``onetick.py.agg.sum()``.
  * **bucket_end_condition** (*condition* *,* *default=None*) – 

    An expression that is evaluated on every tick. If it evaluates to “True”, then a new bucket is created.
    This parameter is only used if `bucket_units` is set to “flexible”.

    Also can be set via `bucket_interval` parameter by passing ``Operation`` object.
  * **end_condition_per_group** (*bool* *,* *default=False*) – 

    Controls application of `bucket_end_condition` in groups.
    * `end_condition_per_group` = True

      `bucket_end_condition` is applied only to the group defined by `group_by`
    * `end_condition_per_group` = False

      `bucket_end_condition` applied across all groups

    This parameter is only used if `bucket_units` is set to “flexible”.

    When set to True, applies to all bucketing conditions. Useful, for example, if you need to specify `group_by`,
    and you want to group items first, and create buckets after that.
  * **boundary_tick_bucket** (*Literal* *[* *'new'* *,*  *'previous'* *]* *,* *default=new*) – 

    Controls boundary tick ownership.
    * **previous**

      A tick on which `bucket_end_condition` evaluates to “true” belongs to the bucket being closed.
    * **new**

      tick belongs to the new bucket.

    This parameter is only used if `bucket_units` is set to “flexible”
  * **group_by** (*list* *,* *str* *or* *expression* *,* *default=None*) – When specified, each bucket is broken further into additional sub-buckets based on specified field values.
    If ``Operation`` is used then GROUP_{i} column is added. Where i is index in group_by list.
    For example, if Operation is the only element in `group_by` list then GROUP_0 field will be added.
  * **groups_to_display** (*Literal* *[* *'all'* *,*  *'previous'* *]* *,* *default=all*) – Specifies for which sub-buckets (groups) ticks should be shown for each bucket interval.
    By default **all** groups are shown at the end of each bucket interval.
    If this parameter is set to **event_in_last_bucket**, only the groups that received at least one tick
    within a given bucket interval are shown.
* **Return type:**
  ``Source``

##### Examples

Calculating delay between the same sources will result in DELAY_MSEC equal to 0.0 and CORRELATION equal to 1.0:
(Note that correlation method may return NaN values for smaller buckets):

```python
import os
trd = otp.CSV(os.path.join(csv_path, 'trd.csv'))
other = trd.deepcopy()
data = trd.estimate_ts_delay(other, 'PRICE', 'PRICE', bucket_interval=10, bucket_time='start')
df = otp.run(data, start=otp.dt(2003, 12, 1, 9), end=otp.dt(2003, 12, 1, 10))
print(df)
```

```
                   Time  DELAY_MSEC  CORRELATION
0   2003-12-01 09:00:00         0.0          1.0
1   2003-12-01 09:00:10         0.0          1.0
2   2003-12-01 09:00:20         0.0          1.0
3   2003-12-01 09:00:30         0.0          1.0
4   2003-12-01 09:00:40         0.0          1.0
..                  ...         ...          ...
355 2003-12-01 09:59:10         0.0          1.0
356 2003-12-01 09:59:20         0.0          1.0
357 2003-12-01 09:59:30         NaN          NaN
358 2003-12-01 09:59:40         0.0          1.0
359 2003-12-01 09:59:50         0.0          1.0

[360 rows x 3 columns]
```

Try changing timestamps of other time-series to see how delay values are changed:

```python
import os
trd = otp.CSV(os.path.join(csv_path, 'trd.csv'))
other = trd.deepcopy()
other['TIMESTAMP'] += otp.Milli(5)
data = trd.estimate_ts_delay(other, 'PRICE', 'PRICE', bucket_interval=10, bucket_time='start')
df = otp.run(data, start=otp.dt(2003, 12, 1, 9), end=otp.dt(2003, 12, 1, 10))
print(df)
```

```
                   Time  DELAY_MSEC  CORRELATION
0   2003-12-01 09:00:00        -5.0          1.0
1   2003-12-01 09:00:10        -5.0          1.0
2   2003-12-01 09:00:20        -5.0          1.0
3   2003-12-01 09:00:30        -5.0          1.0
4   2003-12-01 09:00:40        -5.0          1.0
..                  ...         ...          ...
355 2003-12-01 09:59:10        -5.0          1.0
356 2003-12-01 09:59:20        -5.0          1.0
357 2003-12-01 09:59:30         NaN          NaN
358 2003-12-01 09:59:40        -5.0          1.0
359 2003-12-01 09:59:50        -5.0          1.0

[360 rows x 3 columns]
```

Try filtering out some ticks from other time-series to see how delay and correlation values are changed:

```python
import os
trd = otp.CSV(os.path.join(csv_path, 'trd.csv'))
other = trd.deepcopy()
other = other[::2]
data = trd.estimate_ts_delay(other, 'PRICE', 'PRICE', bucket_interval=10, bucket_time='start')
df = otp.run(data, start=otp.dt(2003, 12, 1, 9), end=otp.dt(2003, 12, 1, 10))
print(df)
```

```
                   Time  DELAY_MSEC  CORRELATION
0   2003-12-01 09:00:00         0.0     1.000000
1   2003-12-01 09:00:10         0.0     1.000000
2   2003-12-01 09:00:20         0.0     1.000000
3   2003-12-01 09:00:30         0.0     0.999115
4   2003-12-01 09:00:40     -1000.0     0.706111
..                  ...         ...          ...
355 2003-12-01 09:59:10         0.0     0.983786
356 2003-12-01 09:59:20         0.0     1.000000
357 2003-12-01 09:59:30         NaN          NaN
358 2003-12-01 09:59:40      -306.0     0.680049
359 2003-12-01 09:59:50         0.0     0.752731

[360 rows x 3 columns]
```

##### SEE ALSO
**ESTIMATE_TS_DELAY** OneTick event processor
