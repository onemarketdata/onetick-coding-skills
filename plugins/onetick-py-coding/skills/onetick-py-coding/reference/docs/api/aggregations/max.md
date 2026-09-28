# otp.agg.max

### ``max(column, running=False, all_fields=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new', group_by=None, groups_to_display='all', time_series_type='event_ts', large_ints=False, null_int_val=0, expect_decimals=None)``

Return maximum value of input `column`

* **Parameters:**
  * **column** (*str* *or* *Column* *or* *Operation*) – String with the name of the column to be aggregated or ``Column`` object.
    ``Operation`` object can also be used – in this case
    the results of this operation for each tick are aggregated
    (see example in `common aggregation examples`).
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
  * **all_fields** (*Union* **[*bool* *,* *str* *]* *,* *default=False*) – 

    See `Aggregation buckets guide` to see examples of how this parameter works.
    * `all_fields` = True

      output ticks include all fields from the input ticks
      * `running` = True

      an output tick is created only when a tick enters the sliding window
      * `running` = False

      fields of first tick in bucket will be used
    * `all_fields` = False and `running` = True

      output ticks are created when a tick enters or leaves the sliding window.
    * `all_fields` = “when_ticks_exit_window” and `running` = True

      output ticks are generated only for exit events, but all attributes from the exiting tick are copied over
      to the output tick and the aggregation is added as another attribute.
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
  * **time_series_type** (*Literal* *[* *'event_ts'* *,*  *'state_ts'* *]* *,* *default=event_ts*) – 

    Controls initial value for each bucket
    * **event_ts**

      only ticks from current bucket used for calculations
    * **state_ts**
      * if there is a tick in bucket with timestamp = bucket start

        only ticks in bucket used for calculation max value
      * else

        latest tick from previous bucket included in current bucket
  * **large_ints** (bool or ``onetick.py.adaptive``, default=False) – 

    This parameter should be set
    if the input field of this aggregation may contain integer values that consist of 15 digits or more.

    Such large integer values cannot be represented by the double type without precision errors
    and thus require special handling.

    If set to **True** , the input field is expected to be a 64-bit integer number.
    The output field will also have 64-bit integer type.
    When no tick belongs to a given time bucket, the output value is set to a minimum of 64-bit integer.

    When this parameter set to ``onetick.py.adaptive``, the aggregation behaves the same way
    as when this parameter is set to True when the input field is a 64-bit integer type,
    and the same way as when this parameter is set to **False** when the input field is not a 64-bit integer type.
  * **null_int_val** (*int* *,* *default=0*) – The value of this parameter is considered to be the equivalent of `NaN`
    when `large_ints` is set to `True` or
    when `large_ints` is set to ``onetick.py.adaptive`` and the input field is a 64-bit integer type.
  * **expect_decimals** (*Union* **[*bool* *,* *str* *,* *NoneType* *]* *,* *default=None*) – 

    This parameter should be set to **True** if the input field of this EP may contain high-precision decimal values.
    Such values require special handling and are represented using the **DECIMAL128** type.
    If set to **True**, the output field will have the **DECIMAL128** type.

    If it’s set to **None** (default), acts as **True** if input column has ``decimal`` type
    (only for OneTick versions which support **EXPECT_DECIMALS** parameter) and as **False** else.

    When this parameter is set to **if_input_val_is_decimal**, the EP behaves the same way
    as when it is set to **True** for input fields of type **DECIMAL128**, and the same way
    as when it is set to **False** for other input types.
    When set to **if_input_val_is_decimal** and the input field type changes during execution
    (e.g., from **DOUBLE** to **DECIMAL128** or from **DECIMAL128** to **DOUBLE**),
    the output field type is switched accordingly.
    Switching from **DECIMAL128** to **DOUBLE** may result in loss of precision.

    Note: `expect_decimals` can be used simultaneously with `large_ints` only if `expect_decimals`
    is set to **if_input_val_is_decimal** and `large_ints` is set to **if_input_val_is_long_integer**.
    In this mode, each parameter adapts to the detected input type.
    If the input becomes **DECIMAL128**, `expect_decimals` applies its logic.
    If the input becomes a 64-bit integer, `large_ints` applies its logic.
    If the input becomes **DOUBLE**, both parameters revert to false behavior.

##### Examples

```
>>> data = otp.Ticks(X=[1, 2, 3, 4])
>>> data = data.agg({'RESULT': otp.agg.max('X')})
>>> otp.run(data)
        Time  RESULT
0 2003-12-04       4
```

##### SEE ALSO
**HIGH** OneTick event processor
