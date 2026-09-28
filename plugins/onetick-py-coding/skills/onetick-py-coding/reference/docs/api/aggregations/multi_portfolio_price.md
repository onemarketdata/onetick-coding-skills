# otp.agg.multi_portfolio_price

#### ``agg.multi_portfolio_price(columns='PRICE', running=False, bucket_interval=0, bucket_time='end', bucket_units=None, bucket_end_condition=None, end_condition_per_group=False, boundary_tick_bucket='new', group_by=None, groups_to_display='all', weight_field_name='', weight_multiplier_field_name='', side='both', weight_type='absolute', portfolios_query_params='', portfolio_value_field_name='VALUE', symbols=None)``

`MULTI_PORTFOLIO_PRICE` aggregation.

For each bucket, computes weighted portfolio price for multiple portfolios.

* **Parameters:**
  * **portfolios_query** (str, ``Source``) – 

    A mandatory parameter that the specifies server-side .otq file that is expected to return
    mandatory columns `PORTFOLIO_NAME` and `SYMBOL_NAME`,
    as well as an optional columns `WEIGHT`, `FX_SYMBOL_NAME` and `FX_MULTIPLY`.

    For a local OneTick server you can pass otp.Source objects.
  * **columns** (*str* *or* *list* *of* *Column* *or* *str* *,* *default=PRICE*) – 

    A list of the names of the input fields for which portfolio value is computed.

    Could be set as a comma-separated list of the names or `list` of name strings/`Columns` objects.
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
  * **weight_field_name** (*Optional* *,* *str* *or* *Column* *,* *default=*) – 

    The name of the field that contains the current value of weight for a member of the portfolio
    that contributed the tick.

    You can also specify weight through the value of symbol parameter `WEIGHT`.

    If `weight_field_name` is specified, all ticks should have the field pointed by this parameter and the value
    of this field is used as the weight.

    If weights are not specified in any of these ways, and you are running a single-stage query,
    the weights take the default value `1`.
  * **weight_multiplier_field_name** (*str* *,* *default=*) – Name of the field, value from which is used for multiplying portfolio value result.
  * **side** (*Literal* *[* *'long'* *,*  *'short'* *,*  *'both'* *]* *,* *default=both*) – 

    When set to **long**, the price of the portfolio is computed only for the input time series with `weight > 0`.

    When set to **short**, the price of the portfolio is computed only for the input time series with `weight < 0`.

    When set to **both**, the price of the portfolio is computed for all input time series.
  * **weight_type** (*Literal* *[* *'absolute'* *,*  *'relative'* *]* *,* *default=absolute*) – 

    When set to `absolute`, the portfolio price is computed as the sum of `input_field_value*weight`
    across all members of the portfolio.

    When set to `relative`, the portfolio price is computed as the sum of
    `input_field_value*weight/sum_of_all_weights` across all members of the portfolio.
  * **portfolios_query_params** (*Union* **[*str* *,* *dict* **[*str* *,* *str* *]* *]* *,* *default=*) – An optional parameter that specifies parameters of the query specified in portfolios_query.
  * **portfolio_value_field_name** (*Union* **[*str* *,* *list* ***Union* *[*[*str* *,* *onetick.py.core.column.Column* *]* *]* *]* *,* *default=VALUE*) – 

    List of the names (string with comma-separated list or `list` of strings/`Columns`) of the output fields
    which contain computed values of the portfolio.

    The number of the field names must match the number of the field names listed in the columns parameter.
  * **symbols** (str, list of str, ``Source``, ``query``, ``eval query``, `onetick.query.GraphQuery`., default=None) – Symbol(s) from which data should be taken.

##### Examples

Basic example, by default this EP takes `PRICE` column as input

```
>>> data = otp.DataSource(
...     'US_COMP', tick_type='TRD', date=otp.dt(2022, 3, 1)
... )
>>> data = data.multi_portfolio_price(
...     portfolios_query='some_query.otq::portfolios_query',
...     symbols=['US_COMP::AAPL', 'US_COMP::MSFT', 'US_COMP::ORCL'],
... )
>>> otp.run(data)  
        Time  VALUE  NUM_SYMBOLS PORTFOLIO_NAME
0 2003-12-01   95.0            3    PORTFOLIO_1
1 2003-12-01   47.5            1    PORTFOLIO_2
2 2003-12-01   32.5            2    PORTFOLIO_3
```

Override `weight` returned by `portfolios_query` with `weight_field_name`

```
>>> data = otp.DataSource(
...     'US_COMP', tick_type='TRD', date=otp.dt(2022, 3, 1)
... )
>>> data['WEIGHT'] = 2
>>> data = data.multi_portfolio_price(
...     portfolios_query='some_query.otq::portfolios_query',
...     weight_field_name='WEIGHT',
...     symbols=['US_COMP::AAPL', 'US_COMP::MSFT', 'US_COMP::ORCL'],
... )
>>> otp.run(data)  
        Time  VALUE  NUM_SYMBOLS PORTFOLIO_NAME
0 2003-12-01   38.0            3    PORTFOLIO_1
1 2003-12-01   19.0            1    PORTFOLIO_2
2 2003-12-01   13.0            2    PORTFOLIO_3
```

Pass parameters to the query from `portfolios_query` via `portfolios_query_params`

```
>>> data = otp.DataSource(
...     'US_COMP', tick_type='TRD', date=otp.dt(2022, 3, 1)
... )
>>> data = data.multi_portfolio_price(
...     portfolios_query='some_query.otq::portfolios_query_with_param',
...     symbols=['US_COMP::AAPL', 'US_COMP::MSFT', 'US_COMP::ORCL'],
...     portfolios_query_params={'PORTFOLIO_1_NAME': 'CUSTOM_NAME'}
... )
>>> otp.run(data)  
        Time  VALUE  NUM_SYMBOLS PORTFOLIO_NAME
0 2003-12-01   95.0            3    CUSTOM_NAME
1 2003-12-01   47.5            1    PORTFOLIO_2
2 2003-12-01   32.5            2    PORTFOLIO_3
```

Use `otp.Source` object as `portfolios_query` (only for local queries)

```
>>> portfolios = otp.Ticks(
...     SYMBOL_NAME=['US_COMP::AAPL', 'US_COMP::MSFT', 'US_COMP::AAPL'],
...     PORTFOLIO_NAME=['PORTFOLIO_1', 'PORTFOLIO_1', 'PORTFOLIO_2'],
...     WEIGHT=[1, 1, 2],
... )
>>> data = otp.DataSource(
...     'US_COMP', tick_type='TRD', date=otp.dt(2022, 3, 1)
... )
>>> data = data.multi_portfolio_price(
...     portfolios_query=portfolios,
...     symbols=['US_COMP::AAPL', 'US_COMP::MSFT'],
... )
>>> otp.run(data)  
        Time  VALUE  NUM_SYMBOLS PORTFOLIO_NAME
0 2003-12-01   47.5            2    PORTFOLIO_1
1 2003-12-01   46.0            1    PORTFOLIO_2
```

##### SEE ALSO
**MULTI_PORTFOLIO_PRICE** OneTick event processor
