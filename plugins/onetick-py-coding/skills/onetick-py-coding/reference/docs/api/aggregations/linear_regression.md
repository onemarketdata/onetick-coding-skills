# otp.agg.linear_regression

### ``linear_regression(dependent_variable_field_name, independent_variable_field_name, running=False, all_fields=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new')``

`LINEAR_REGRESSION` aggregation.

For each bucket, computes the linear regression parameters slope and intercept of specified input fields
`dependent_variable_field_name` and `independent_variable_field_name`.
Adds computed parameters as SLOPE and INTERCEPT fields in output time series.
The relationship between the dependent variable (`Y`) and the independent variable (`X`) is defined
by the formula: Y = SLOPE \* X + INTERCEPT, where SLOPE and INTERCEPT are the calculated output parameters.

* **Parameters:**
  * **dependent_variable_field_name** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]*) – Specifies the attribute used as the dependent variable in the calculation of the slope and intercept.
    The ticks in the input time series must contain this attribute.
  * **independent_variable_field_name** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]*) – Specifies the attribute used as the independent variable in the calculation of the slope and intercept.
    The ticks in the input time series must contain this attribute.
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

##### Examples

```
>>> data = otp.Ticks({'X': [10.0, 9.5, 8.0, 8.5], 'Y': [3.0, 5.0, 4.5, 3.5]})
>>> data = data.linear_regression(
...     dependent_variable_field_name=data['Y'],
...     independent_variable_field_name=data['X'],
... )  
>>> otp.run(data)  
        Time  SLOPE  INTERCEPT
0 2003-12-04   -0.3        6.7
```

##### SEE ALSO
**LINEAR_REGRESSION** OneTick event processor
