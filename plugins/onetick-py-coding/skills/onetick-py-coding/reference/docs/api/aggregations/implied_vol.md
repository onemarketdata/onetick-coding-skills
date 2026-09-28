# otp.agg.implied_vol

### ``implied_vol(column, running=False, all_fields=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new', group_by=None, groups_to_display='all', interest_rate=None, price_field='PRICE', option_price_field='OPTION_PRICE', method='newton', precision='1.0e-5', value_for_non_converge='nan_val', option_type_field='', strike_price_field='', days_in_year=365, days_till_expiration_field='', expiration_date_field='')``

`IMPLIED_VOL` aggregation.

For each bucket, computes implied volatility value for the last tick in the bucket,
based on the Black-Scholes option pricing model.

This EP requires a time series of ticks, having the `PRICE` and `OPTION_PRICE` attributes.

It also requires several parameters to compute the implied volatility.
Those are, `OPTION_TYPE`, `STRIKE_PRICE`, `EXPIRATION_DATE` or `DAYS_TILL_EXPIRATION` and `INTEREST_RATE`.
Each parameter can be specified either via a symbol parameter with the same name, or via a tick field,
by specifying name of that field as an EP parameter.
Besides, `interest_rate` can also be specified as aggregation parameter.
In either case `OPTION_TYPE` must have either `CALL` value, or `PUT`.
`EXPIRATION_DATE` is in `YYYYMMDD` format, a string in case of a symbol parameter and an integer
in case of a tick attribute.

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
  * **interest_rate** (*Union* **[*float* *,* *int* *,* *onetick.py.core.column.Column* *,* *str* *,* *NoneType* *]* *,* *default=None*) – 

    The risk-free interest rate.

    Could be set via a tick field, by specifying name of that field as string or
    passing ``Column`` object.
  * **price_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=PRICE*) – The name of the field carrying the price value.
  * **option_price_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=OPTION_PRICE*) – The name of the field carrying the option price value.
  * **method** (*str* *,* *default=newton*) – 

    Allowed values are `newton`, `newton_with_fallback` and `bisections`.

    Choose between `newton` and `bisections` for finding successively better approximations
    to the implied volatility value.

    Choose `newton_with_fallback` to automatically fall back to `bisections` method
    when `newton` fails to converge.
  * **precision** (*float* *,* *default=1.0e-5*) – Precision of the implied volatility value.
  * **value_for_non_converge** (*str* *,* *default=nan_val*) – 

    Allowed values are `nan_val` and `closest_found_val`, where `closest_found_val` stands
    for the volatility value for which the difference between calculated option price and input option price is minimal.

    Choose between `nan_val` and `closest_found_val` as implied volatility value,
    when the root-finding method does not converge within the specified precision.
  * **option_type_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=*) – Specifies name of the field, which carries the option type (either **CALL** or **PUT**).
  * **strike_price_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=*) – Specifies name of the field, which carries the strike price of the option.
  * **days_in_year** (*int* *,* *default=365*) – Specifies number of days in a year (say, 365 or 252 (business days, etc.).
    Used with `days_till_expiration` parameter to compute the fractional years till expiration.
  * **days_till_expiration_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=*) – Specifies name of the field, which carries number of days till expiration of the option.
  * **expiration_date_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=*) – Specifies name of the field, which carries the expiration date of the option, in **YYYYMMDD** format.

##### Examples

Basic example:

```
>>> data = otp.DataSource('SOME_DB', symbol='AAA', tick_type='TT')  
>>> data = data.implied_vol(
...     interest_rate=0.05, option_type_field=data['OPTION_TYPE'],
...     strike_price_field=data['STRIKE_PRICE'], days_till_expiration_field=data['DAYS_TILL_EXPIRATION'],
... )  
>>> otp.run(data)  
        Time     VALUE
0 2003-12-04  0.889491
```

Specifying `interest_rate` and `strike_price` as symbol parameters:

```
>>> sym = otp.Ticks({
...     'SYMBOL_NAME': ['TEST'],
...     'INTEREST_RATE': [0.05],
...     'STRIKE_PRICE': [100.0],
... })  
>>> data = otp.DataSource('SOME_DB', symbol='AAA', tick_type='TT')  
>>> data = data.implied_vol(
...     option_type_field=data['OPTION_TYPE'], days_till_expiration_field=data['DAYS_TILL_EXPIRATION'],
... )  
>>> otp.run(data)  
        Time     VALUE
0 2003-12-04  0.889491
```

##### SEE ALSO
**IMPLIED_VOL** OneTick event processor
