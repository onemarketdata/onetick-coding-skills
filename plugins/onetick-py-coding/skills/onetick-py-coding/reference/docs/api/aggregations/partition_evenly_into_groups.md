# otp.agg.partition_evenly_into_groups

### ``partition_evenly_into_groups(field_to_partition, weight_field, number_of_groups, running=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, boundary_tick_bucket='new')``

`PARTITION_EVENLY_INTO_GROUPS` aggregation.

> For each bucket, this EP breaks ticks into the specified number of groups (`number_of_groups`)
> by the specified field (`field_to_partition`) in a way that the sums
> of the specified weight fields (`weight_field`) in each group are as close as possible.
* **Parameters:**
  * **field_to_partition** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]*) – Specifies the tick field that will be partitioned.
  * **weight_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]*) – Specifies the tick field, the values of which are evaluated as weight; and then, the partitioning is be applied,
    so that the total weight of the groups are as close as possible.
  * **number_of_groups** (*int*) – Specifies the target number of partitions to which the tick should be divided.
  * **running** (*bool* *,* *default=False*) – 

    See `Aggregation buckets guide` to see examples of how this parameter works.

    Specifies if the aggregation will be calculated as a sliding window.
    `running` and `bucket_interval` parameters determines when new buckets are created.
    * `running` = True

      aggregation will be calculated in a sliding window.
      * `bucket_interval` = N (N > 0)

        Window size will be N. Output tick will be generated when tick “enter” window (**arrival event**) and
        when “exit” window (**exit event**)
      * `bucket_interval` = 0

        Left boundary of window will be set to query start time. For each tick aggregation will be calculated in
        the interval [start_time; tick_t] from query start time to the tick’s timestamp (inclusive).
    * `running` = False (default)

      buckets partition the [query start time, query end time) interval into non-overlapping intervals
      of size `bucket_interval` (with the last interval possibly of a smaller size).
      If `bucket_interval` is set to **0** a single bucket for the entire interval is created.

      Note that in non-running mode OneTick unconditionally divides the whole time interval
      into specified number of buckets.
      It means that you will always get this specified number of ticks in the result,
      even if you have less ticks in the input data.

    Default: False
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
  * **boundary_tick_bucket** (*Literal* *[* *'new'* *,*  *'previous'* *]* *,* *default=new*) – 

    Controls boundary tick ownership.
    * **previous**

      A tick on which `bucket_end_condition` evaluates to “true” belongs to the bucket being closed.
    * **new**

      tick belongs to the new bucket.

    This parameter is only used if `bucket_units` is set to “flexible”

##### Examples

```
>>> data = otp.Ticks(X=['A', 'B', 'A', 'C', 'D'], SIZE=[10, 30, 20, 15, 14])
>>> data = data.partition_evenly_into_groups(
...     field_to_partition=data['X'],
...     weight_field=data['SIZE'],
...     number_of_groups=3,
... )
>>> otp.run(data)
        Time FIELD_TO_PARTITION  GROUP_ID
0 2003-12-04                  A         0
1 2003-12-04                  B         1
2 2003-12-04                  C         2
3 2003-12-04                  D         2
```

##### SEE ALSO
**PARTITION_EVENLY_INTO_GROUPS** OneTick event processor
