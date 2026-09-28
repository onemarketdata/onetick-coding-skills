# otp.agg.percentile

### ``percentile(number_of_quantiles, input_field_names, output_field_names, running=False, bucket_interval=0, bucket_units=None, bucket_time='end', bucket_end_condition=None, boundary_tick_bucket='new', group_by=None, groups_to_display='all', end_condition_per_group=False)``

Percentile **running** aggregation.

For each bucket, propagates its `n-1` `n-quantiles` where a comparison between ticks is done
using a specified set of tick fields. A new field (`QUANTILE`) with the quantile number is added.

* **Parameters:**
  * **number_of_quantiles** (*int*) – 

    Specifies the number `n` of quantiles. Setting it to 2 will propagate only one tick - the median.

    Default: 2
  * **input_field_names** (*list* ***Union* *[**Union* *[*[*str* *,* *onetick.py.Column* *]* *,* *tuple* ***Union* *[*[*str* *,* *onetick.py.Column* *]* *,* *str* *]* *]* *]*) – 

    List of numeric field names to run aggregation on.
    You can use as list elements either string column name, either ``Columns``.

    You can change default comparison order (`desc`) for the field,
    by passing a tuple of column name and comparison order (`desc` or `asc`) instead column name.
  * **output_field_names** (*Optional* **[*list* **[*str* *]* *]*) – 

    Output columns name in the same order as columns from `input_field_names`

    `output_field_names` and `input_field_names` must have the same number of fields.

    If not set, `output_field_names` will be equal to `input_field_names`.
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
  * **bucket_units** (*Optional* *[**Literal* *[* *'seconds'* *,*  *'ticks'* *,*  *'days'* *,*  *'months'* *,*  *'flexible'* *]* *]* *,* *default=None*) – 

    Set bucket interval units.

    By default, if `bucket_units` and `bucket_end_condition` not specified, set to **seconds**.
    If `bucket_end_condition` specified, then `bucket_units` set to **flexible**.

    If set to **flexible** then `bucket_end_condition` must be set.

    Note that **seconds** bucket unit doesn’t take into account daylight-saving time of the timezone,
    so you may not get expected results when using, for example, 24 \* 60 \* 60 seconds as bucket interval.
    In such case use **days** bucket unit instead.
    See example in ``onetick.py.agg.sum()``.
  * **bucket_time** (*Literal* *[* *'start'* *,*  *'end'* *]* *,* *default=end*) – 

    Control output timestamp.
    * **start**

      the timestamp  assigned to the bucket is the start time of the bucket.
    * **end**

      the timestamp assigned to the bucket is the end time of the bucket.
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
  * **group_by** (*list* *,* *str* *or* *expression* *,* *default=None*) – When specified, each bucket is broken further into additional sub-buckets based on specified field values.
    If ``Operation`` is used then GROUP_{i} column is added. Where i is index in group_by list.
    For example, if Operation is the only element in `group_by` list then GROUP_0 field will be added.
  * **groups_to_display** (*Literal* *[* *'all'* *,*  *'previous'* *]* *,* *default=all*) – Specifies for which sub-buckets (groups) ticks should be shown for each bucket interval.
    By default **all** groups are shown at the end of each bucket interval.
    If this parameter is set to **event_in_last_bucket**, only the groups that received at least one tick
    within a given bucket interval are shown.
  * **end_condition_per_group** (*bool* *,* *default=False*) – 

    Controls application of `bucket_end_condition` in groups.
    * `end_condition_per_group` = True

      `bucket_end_condition` is applied only to the group defined by `group_by`
    * `end_condition_per_group` = False

      `bucket_end_condition` applied across all groups

    This parameter is only used if `bucket_units` is set to “flexible”.

    When set to True, applies to all bucketing conditions. Useful, for example, if you need to specify `group_by`,
    and you want to group items first, and create buckets after that.

##### Examples

```
>>> t = otp.Ticks({'A': [1, 2, 3, 4]})
>>> t = t.percentile(['A'])
>>> otp.run(t)
        Time  A  QUANTILE
0 2003-12-04  2         1
```

You can also pass column:

```
>>> t = otp.Ticks({'A': [1, 2, 3, 4]})
>>> t = t.percentile([t['A']])
>>> otp.run(t)
        Time  A  QUANTILE
0 2003-12-04  2         1
```

Change number of quantiles:

```
>>> t = otp.Ticks({'A': [1, 2, 3, 4]})
>>> t = t.percentile(['A'], number_of_quantiles=3)
>>> otp.run(t)
        Time  A  QUANTILE
0 2003-12-04  3         1
1 2003-12-04  2         2
```

Or change default comparison order:

```
>>> t = otp.Ticks({'A': [1, 2, 3, 4]})
>>> t = t.percentile([('A', 'asc')], number_of_quantiles=3)
>>> otp.run(t)
        Time  A  QUANTILE
0 2003-12-04  2         1
1 2003-12-04  3         2
```

You can also change output column name via `output_field_names` parameter:

```
>>> t = otp.Ticks({'A': [1, 2, 3, 4]})
>>> t = t.percentile(['A'], output_field_names=['B'])
>>> otp.run(t)
        Time  B  QUANTILE
0 2003-12-04  2         1
```

##### SEE ALSO
**PERCENTILE** OneTick event processor
