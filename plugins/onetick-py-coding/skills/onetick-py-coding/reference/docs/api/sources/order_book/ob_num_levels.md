# otp.ObNumLevels

### ``ObNumLevels(running=False, bucket_interval=0, side=None, book_uncross_method=None, dq_events_that_clear_book=None, max_initialization_days=1, db=None, symbol=<class 'onetick.py.utils.types.adaptive'>, tick_type=<class 'onetick.py.utils.types.adaptive'>, start=<class 'onetick.py.utils.types.adaptive'>, end=<class 'onetick.py.utils.types.adaptive'>, date=None, schema_policy=<class 'onetick.py.utils.types.adaptive'>, guess_schema=None, identify_input_ts=False, back_to_first_tick=0, keep_first_tick_timestamp=None, max_back_ticks_to_prepend=1, where_clause_for_back_ticks=None, symbols=None, presort=<class 'onetick.py.utils.types.adaptive'>, batch_size=None, concurrency=<class 'onetick.py.utils.types.default'>, schema=None, symbol_date=None, query_parameters=None, **kwargs)``

Construct a source providing the number of levels in the order book for a given `db`.
This is just a shortcut for
``DataSource`` + ``ob_num_levels()``.

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
  * **bucket_interval** (int or `datetime offset object`, default=0) – 

    Determines the length of each bucket in seconds.

    Bucket interval can be set via `datetime offset objects`
    like ``otp.Second``, ``otp.Minute``,
    ``otp.Hour``, ``otp.Day``.
    In this case it will be converted to seconds.
  * **side** (*Literal* *[* *'ASK'* *,*  *'BID'* *]* *,* *default=None*) – Specifies whether the function is to be applied to sell orders (ASK), buy orders (BID), or both (empty).
  * **book_uncross_method** (*Literal* *[* *'REMOVE_OLDER_CROSSED_LEVELS'* *]* *,* *default=None*) – When set to “REMOVE_OLDER_CROSSED_LEVELS”, all ask levels that have price lower or equal to
    the price of a new bid tick get removed from the book, and all bid levels that have price higher or equal
    to the price of a new ask tick get removed from the book.
  * **dq_events_that_clear_book** (*list* **[*str* *]* *,* *default=None*) – A list of names of data quality events arrival of which should clear the order book.
  * **max_initialization_days** (*int* *,* *default=1*) – This parameter specifies how many days back book event processors should go in order to find
    the latest full state of the book.
    The query will not go back resulting number of days if it finds initial book state earlier.
    When book event processors are used after VIRTUAL_OB EP, this parameter should be set to 0.
    When set, this parameter takes precedence over the configuration parameter BOOKS.MAX_INITIALIZATION_DAYS.
  * **db** (str, list of str, ``otp.DB``, default=None) – 

    Name(s) of the database or the database object(s).

    When passing a single database, the tick type can be embedded in the name
    using `'DB_NAME::TICK_TYPE'` format (e.g., `'US_COMP::TRD'`).

    When passing a list of databases, each entry can include its own tick type
    (e.g., `['US_COMP::TRD', 'CME::QTE']`). If some entries lack a tick type,
    the `tick_type` parameter is used to fill them in.

    When `None`, the database is expected to come as part of the symbol name
    (e.g., `'DB::SYMBOL'`), and `tick_type` must be set explicitly.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``, default= ``onetick.py.adaptive``) – Symbol(s) from which data should be taken.
  * **tick_type** (str, list of str, default= ``onetick.py.adaptive``) – 

    Tick type of the data (e.g., `'TRD'` for trades, `'QTE'` for quotes).

    When ``adaptive`` (default), the tick type is auto-detected from the
    database. If auto-detection fails or multiple databases are specified, defaults to `'TRD'`.

    Can be a list of strings (e.g., `['TRD', 'QTE']`) to merge multiple tick types
    from the same database into a single data flow.
  * **start** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – Start of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **end** (``datetime.datetime``, ``otp.datetime``, ``onetick.py.adaptive``, default= ``onetick.py.adaptive``) – End of the interval from which the data should be taken.
    Default is ``onetick.py.adaptive``, making the final query deduce the time
    limits from the rest of the graph.
  * **date** (``datetime.datetime``, ``otp.datetime``, default=None) – Allows to specify a whole day instead of passing explicitly `start` and `end` parameters.
    If it is set along with the `start` and `end` parameters then last two are ignored.
  * **schema_policy** (‘tolerant’, ‘tolerant_strict’, ‘fail’, ‘fail_strict’, ‘manual’, ‘manual_strict’, default= ``onetick.py.adaptive``) – 

    Schema deduction policy.

    See `the schema concept guide` for more details about how data schema works in onetick-py.

    Default schema policy is ``adaptive``:
    - If database is specified with `db` parameter, then default schema policy is set to ‘tolerant’
      and automatic schema deduction is enabled
      (additional query will be called to get the schema from the database!).
    - If only `tick_type` or `symbols` parameters are set, then default schema policy is set to ‘manual’.

    Default schema policy can be changed with
    ``otp.config.default_schema_policy``
    configuration parameter.

    If parameter `schema` is set, then `schema_policy` will be automatically set to `manual`
    (unless it’s not set to other value).

    If deprecated parameter `guess_schema` is set to True then default value is ‘fail’, if False then ‘manual’.
    If `schema_policy` is set to `None` then default value is ‘tolerant’.

    Supported parameter values:
    - ’tolerant’
      Additional query will be called to get the schema from the database.
      The resulting ``otp.Source.schema`` is a combination of parameter `schema`
      and the values from the database.
      Database schema is checked to be type-compatible with parameter `schema`,
      and ValueError is raised if checks are failed.
      Also, with this policy database is scanned 5 days back to find the schema.
      It is useful when database is misconfigured or in case of holidays.
    - ’tolerant_strict’
      Additional query will be called to get the schema from the database.
      The resulting ``otp.Source.schema``
      will be set to parameter `schema` if it’s not empty.
      Otherwise, schema from the database is used.
      Database schema is checked if it lacks fields from the parameter `schema`
      and it’s checked to be type-compatible with parameter `schema`
      and ValueError is raised if checks are failed.
      Also, with this policy database is scanned 5 days back to find the schema.
      It is useful when database is misconfigured or in case of holidays.
    - ’fail’
      The same as ‘tolerant’, but if the database schema can’t be deduced, raises an Exception.
    - ’fail_strict’
      The same as ‘tolerant_strict’, but if the database schema can’t be deduced, raises an Exception.
    - ’manual’
      The resulting ``otp.Source.schema`` will be set to parameter `schema`.
      Compatibility with database schema will not be checked.
      If some fields are not specified in `schema`, but exist in the database, they will not be dropped
      and will be available in the results of ``otp.run`` unless they are dropped with
      other source methods.
    - ’manual_strict’
      The resulting ``otp.Source.schema`` will be exactly `schema`,
      other columns will be dropped from result if they exist in the database.
      Compatibility with database schema will not be checked.

    If some fields specified in `schema` do not exist in the database,
    their values will be set to some default value for a type
    (0 for integers, NaNs for floats, empty string for strings, epoch for datetimes).
  * **guess_schema** (*bool* *,* *default=None*) – 

    #### Deprecated
    Deprecated since version 1.3.16.

    Use `schema_policy` parameter instead.

    If `guess_schema` is set to True then `schema_policy` value is ‘fail’, if False then ‘manual’.
  * **identify_input_ts** (*bool* *,* *default=False*) – If True, adds `SYMBOL_NAME` and `TICK_TYPE` fields to every output tick,
    identifying which symbol and tick type each tick came from.
    Especially useful when merging multiple symbols to distinguish the source of each tick.
  * **back_to_first_tick** (int, `offset`, `otp.expr`, ``Operation``, default=0) – 

    Determines how far back (in seconds) to search for the latest tick before `start` time.
    If one is found, it is prepended to the output with its timestamp changed to `start` time.
    This is useful for initializing state (e.g., getting the last known price before market open).

    Accepts an integer (seconds), a time offset like `otp.Day(1)` or `otp.Hour(2)`,
    or an `otp.expr` for dynamic values.

    Note: the value is rounded to whole seconds, so `otp.Millis(999)` becomes 0.
    Use with `keep_first_tick_timestamp` to preserve the original tick time,
    and `max_back_ticks_to_prepend` to retrieve more than one historical tick.
  * **keep_first_tick_timestamp** (*str* *,* *default=None*) – 

    Name for a new ``nsectime`` field that stores
    the original timestamp of prepended ticks. For ticks within the query interval,
    this field equals the `Time` field. For ticks prepended by `back_to_first_tick`,
    it contains their true historical timestamp (before it was overwritten with `start` time).

    This parameter is ignored if `back_to_first_tick` is 0.
  * **max_back_ticks_to_prepend** (*int* *,* *default=1*) – 

    Maximum number of the most recent ticks before `start` time to prepend to the output.
    Only used when `back_to_first_tick` is non-zero. All prepended ticks have their
    timestamp changed to `start` time. Must be at least 1.

    For example, to get the last 5 trades before market open, set `back_to_first_tick=otp.Day(1)`
    and `max_back_ticks_to_prepend=5`.
  * **where_clause_for_back_ticks** (*onetick.py.core.column_operations.base.Raw* *,* *default=None*) – 

    A filter expression applied only to ticks found during the backward search
    (controlled by `back_to_first_tick`). Ticks where this expression evaluates to
    False are skipped and not prepended.

    Must be an `otp.raw` expression with `dtype=bool`.
    For example, `otp.raw('SIZE>=100', dtype=bool)` keeps only ticks with SIZE >= 100.
  * **symbols** (str, list of str, ``Source``, ``query``, ``eval query``, `onetick.query.GraphQuery`., default=None) – Symbol(s) from which data should be taken.
    Alias for `symbol` parameter. Will take precedence over it.
  * **presort** (bool, default= ``onetick.py.adaptive``) – 

    Controls whether to use a PRESORT Event Processor when querying multiple bound symbols.
    PRESORT parallelizes data fetching across symbols and merges results in timestamp order,
    which is generally faster than sequential MERGE for large symbol lists.

    Applicable only when `symbols` is set. By default, True when `symbols` is set,
    False otherwise. Set to False to use sequential MERGE instead.
  * **batch_size** (*int* *,* *default=None*) – 

    Number of symbols to process in each batch during `presort` execution.
    Larger batch sizes reduce overhead but use more memory. Only applicable when `presort` is True.

    By default, the value from
    ``otp.config.default_batch_size`` is used.
  * **concurrency** (int, default= `onetick.py.utils.default`) – 

    Specifies the number of CPU cores to utilize for the `presort`.
    By default, the value is inherited from the value of the query where this PRESORT is used.

    For the main query it may be specified in the `concurrency` parameter of ``run()`` method
    (which by default is set to
    ``otp.config.default_concurrency``).

    For the auxiliary queries (like first-stage queries) empty value means OneTick’s default of 1.
    If ``otp.config.presort_force_default_concurrency``
    is set then default concurrency value will be set in all PRESORT EPs in all queries.
  * **schema** (*Optional* **[*dict* **[*str* *,* *type* *]* *]* *,* *default=None*) – 

    Dict of column name to column type pairs that the source is expected to have.

    Supported types: `int`, `float`, `str`, [`otp.string[N]`](../../types/string.md#onetick.py.string),
    `otp.varstring[N]`,
    ``otp.nsectime``,
    ``otp.msectime``,
    ``otp.decimal``, `bytes`.

    If the type of a column is irrelevant, provide `None` as the type.

    How the schema is used depends on `schema_policy`. When `schema` is set and
    `schema_policy` is not explicitly provided, `schema_policy` defaults to `'manual'`.
  * **symbol_date** (``otp.datetime`` or ``datetime.datetime`` or int, default=None) – 

    Date used for symbol resolution in date-dependent symbologies,
    where the same symbol identifier can map to different instruments on different dates.

    Accepts ``otp.datetime``, ``datetime.datetime``,
    or an integer in the `YYYYMMDD` format (e.g., `20220301`).

    Can only be specified when `symbols` is set. If `symbols` is a plain list of strings,
    it is internally converted to a first-stage query with the given `symbol_date`.
  * **query_parameters** (``otp.QueryParameters``, default=None) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** (*type* **[*str* *]*) – Deprecated. Use `schema` instead.
    List of <column name> -> <column type> pairs that the source is expected to have.
    If the type is irrelevant, provide None as the type in question.

##### Examples

```
>>> data = otp.ObNumLevels(db='CME_SAMPLE', tick_type='PRL_FULL', symbols=r'NQ\H24',
...                        bucket_interval=otp.Second(300))                  
>>> otp.run(data, start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 11))  
                  Time  ASK_VALUE  BID_VALUE
0  2024-02-01 10:05:00      743.0      830.0
1  2024-02-01 10:10:00      753.0      820.0
2  2024-02-01 10:15:00      741.0      831.0
...
```

Consolidated book across multiple venues:

```
>>> data = otp.ObNumLevels(db=['ARCA', 'AMEX', 'NASDAQ', 'NYSE'], symbols='AA',      
...                        tick_type='PRL_FULL')                                     
>>> otp.run(data, start=otp.dt(2026, 6, 3, 12), end=otp.dt(2026, 6, 3, 12))          
                 Time  ASK_VALUE  BID_VALUE
0 2026-06-03 12:00:00      442.0      968.0
```

##### SEE ALSO
``onetick.py.DataSource``

``onetick.py.Source.ob_num_levels()``

``onetick.py.agg.ob_num_levels()``

**OB_NUM_LEVELS** OneTick event processor

