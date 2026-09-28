# otp.agg.ob_size

### ``ob_size(running=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, group_by=None, groups_to_display='all', side=None, max_levels=None, max_depth_for_price=None, max_spread=None, book_uncross_method=None, dq_events_that_clear_book=None, max_initialization_days=1, best_ask_price_field=None, best_bid_price_field=None)``

Returns the total size for a specified number of order book levels at the end of each bucket interval.

* **Parameters:**
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
  * **bucket_units** (*Optional* *[**Literal* *[* *'seconds'* *,*  *'days'* *,*  *'months'* *,*  *'flexible'* *]* *]* *,* *default=None*) – 

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
  * **group_by** (*list* *,* *str* *or* *expression* *,* *default=None*) – When specified, each bucket is broken further into additional sub-buckets based on specified field values.
    If ``Operation`` is used then GROUP_{i} column is added. Where i is index in group_by list.
    For example, if Operation is the only element in `group_by` list then GROUP_0 field will be added.
  * **groups_to_display** (*Literal* *[* *'all'* *,*  *'previous'* *]* *,* *default=all*) – Specifies for which sub-buckets (groups) ticks should be shown for each bucket interval.
    By default **all** groups are shown at the end of each bucket interval.
    If this parameter is set to **event_in_last_bucket**, only the groups that received at least one tick
    within a given bucket interval are shown.
  * **side** (*Literal* *[* *'ASK'* *,*  *'BID'* *]* *,* *default=None*) – Specifies whether the function is to be applied to sell orders (ASK), buy orders (BID), or both (empty).
  * **max_levels** (*int* *,* *default=None*) – Number of order book levels (between 1 and 100_000) that need to be computed.
    If empty, all levels will be computed.
  * **max_depth_for_price** (*float* *,* *default=None*) – The multiplier, product of which with the price at the top level of the book determines maximum price distance
    from the top of the book for the levels that are to be included into the book.
    In other words, only bids at <top_price>\*(1-max_depth_for_price) and above
    and only asks of <top_price>\*(1+\`max_depth_for_price\`) and less will be returned.
    If the number of the levels that are to be included into the book, according to this criteria,
    exceeds max_levels, only max_levels levels of the book will be returned.
  * **max_spread** (*float* *,* *default=None*) – An absolute value, price levels with price that satisfies `abs(<MID price> - <order price>) <= max_spread/2`
    contribute to computed book. If `max_spread` is specified, `side` should not be specified.
    Empty book is returned when one side is empty.
  * **book_uncross_method** (*Literal* *[* *'REMOVE_OLDER_CROSSED_LEVELS'* *]* *,* *default=None*) – When set to “REMOVE_OLDER_CROSSED_LEVELS”, all ask levels that have price lower or equal to
    the price of a new bid tick get removed from the book, and all bid levels that have price higher or equal
    to the price of a new ask tick get removed from the book.
  * **dq_events_that_clear_book** (*list* **[*str* *]* *,* *default=None*) – A list of names of data quality events arrival of which should clear the order book.
  * **max_initialization_days** (*int* *,* *default=1*) – This parameter specifies how many days back book event processors should go in order to find
    the latest full state of the book.
    The query will not go back resulting number of days if it finds initial book state earlier.
    When book event processors are used after VIRTUAL_OB EP, this parameter should be set to 0.
    When set, this parameter takes precedence over the configuration parameter BOOKS.MAX_INITIALIZATION_DAYS.
  * **best_ask_price_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=None*) – If specified, this parameter represents the name of the field value of which represents the lowest ask price
    starting from which the book ask size is to be computed.
    This value would also be used as the top price, relative to which `max_depth_for_price` would be computed.
  * **best_bid_price_field** (*Union* **[*str* *,* *onetick.py.core.column.Column* *]* *,* *default=None*) – If specified, this parameter represents the name of the field value of which represents the highest bid price
    starting from which the book bid size is to be computed.
    This value would also be used as the top price, relative to which `max_depth_for_price` would be computed.

##### Examples

Basic example:

```
>>> data = otp.DataSource(db='CME_SAMPLE', tick_type='PRL_FULL', symbols=r'NQ\H24')  
>>> data = otp.agg.ob_size(bucket_interval=otp.Minute(5), max_levels=3).apply(data)  
>>> otp.run(data, start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 11))          
                  Time  ASK_VALUE  BID_VALUE
0  2024-02-01 10:05:00       12.0       10.0
1  2024-02-01 10:10:00       12.0        5.0
2  2024-02-01 10:15:00       11.0       13.0
...
```

Selecting side via `side` parameter:

```
>>> data = otp.DataSource(db='CME_SAMPLE', tick_type='PRL_FULL', symbols=r'NQ\H24')              
>>> data = otp.agg.ob_size(bucket_interval=otp.Minute(5), max_levels=3, side='ASK').apply(data)  
>>> otp.run(data, start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 11))                      
                  Time  VALUE
0  2024-02-01 10:05:00   12.0
1  2024-02-01 10:10:00   12.0
2  2024-02-01 10:15:00   11.0
...
```

##### SEE ALSO
``onetick.py.ObSize()``

**OB_SIZE** OneTick event processor

