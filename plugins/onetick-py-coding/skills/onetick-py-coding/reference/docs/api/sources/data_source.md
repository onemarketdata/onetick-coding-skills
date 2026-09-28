# otp.DataSource

### ``class DataSource(db=None, symbol=utils.adaptive, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, date=None, schema_policy=None, guess_schema=utils.adaptive, identify_input_ts=None, back_to_first_tick=False, keep_first_tick_timestamp=0, max_back_ticks_to_prepend=None, where_clause_for_back_ticks=1, symbols=None, presort=None, batch_size=utils.adaptive, concurrency=None, schema=utils.default, symbol_date=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Construct a source providing data from a given `db`.

#### WARNING
Default value of the parameter `schema_policy` enables automatic deduction
of the data schema, but it is highly not recommended for production code.
For details see `Schema deduction mechanism`.

* **Parameters:**
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

    Supported types: `int`, `float`, `str`, [`otp.string[N]`](../types/string.md#onetick.py.string),
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

#### NOTE
If interval that was set for ``DataSource`` via `start`/`end` or `date` parameters
does not match intervals in other ``Source`` objects used in query,
or does not match the whole query interval, then `modify_query_times()` will be applied
to this `DataSource` with specified interval as start and end times parameters.

If `symbols` parameter is omitted, you need to specify unbound symbols for the query in `symbols`
parameter of ``onetick.py.run()`` function.

If `symbols` parameter is set, ``otp.merge`` is used to merge all passed bound symbols.
In this case you don’t need to specify unbound symbols in ``onetick.py.run()`` call.

It’s not allowed to specify bound and unbound symbols at the same time.

##### Examples

Query a single symbol from a database, specifying `db` as a string:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data[['PRICE']][:3]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
```

Note that default schema policy is **tolerant** and
*additional query will be called to get the schema from the database*:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data.schema
{'COND': string[4],
 'CORR': <class 'onetick.py.types._int'>,
 'DELETED_TIME': <class 'onetick.py.types.msectime'>,
 'EXCHANGE': string[1],
 'OMDSEQ': <class 'onetick.py.types.uint'>,
 'PARTICIPANT_TIME': <class 'onetick.py.types.nsectime'>,
 'PRICE': <class 'float'>,
 'SEQ_NUM': <class 'int'>,
 'SIZE': <class 'int'>,
 'SOURCE': string[1],
 'STOP_STOCK': string[1],
 'TICKER': string[16],
 'TICK_STATUS': <class 'onetick.py.types._int'>,
 'TRADE_ID': string[20],
 'TRF': string[1],
 'TRF_TIME': <class 'onetick.py.types.nsectime'>,
 'TTE': string[1]}
```

**manual** schema policy allows setting `schema` manually and disables getting schema from the database:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', schema_policy='manual')
>>> data.schema
{}
```

But the fields from the database will still be available in results:

```
>>> otp.run(data[:1], date=otp.dt(2024, 2, 1))  
                           Time EXCHANGE  COND STOP_STOCK SOURCE TRF TTE TICKER  PRICE        DELETED_TIME ...
0 2024-02-01 04:00:00.008283417        K  @ TI                 N       0   AAPL  186.5 1969-12-31 19:00:00 ...
```

If parameter `schema` is set, schema policy is set to **manual** automatically
and can be omitted from the previous example:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', schema={})
>>> otp.run(data[:1], date=otp.dt(2024, 2, 1))  
                           Time EXCHANGE  COND STOP_STOCK SOURCE TRF TTE TICKER  PRICE        DELETED_TIME ...
0 2024-02-01 04:00:00.008283417        K  @ TI                 N       0   AAPL  186.5 1969-12-31 19:00:00 ...
```

Schema policy **manual_strict** uses exactly the provided `schema`,
and only these fields will be available in results.
Fields that don’t exist in the database get default values
(0 for `int`, NaN for `float`, empty string for `str`):

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL',
...                       schema_policy='manual_strict',
...                       schema={'PRICE': float, 'CUSTOM_FLAG': int})
>>> data.schema
{'PRICE': <class 'float'>, 'CUSTOM_FLAG': <class 'int'>}
>>> otp.run(data[:1], date=otp.dt(2024, 2, 1))
                           Time  PRICE  CUSTOM_FLAG
0 2024-02-01 04:00:00.008283417  186.5            0
```

`db` can be a list to merge data from multiple databases.
Each entry can embed its tick type using `'DB_NAME::TICK_TYPE'` format:

```python
data = otp.DataSource(
    db=['US_COMP_SAMPLE::TRD', 'CME_SAMPLE::TRD'],
    symbols='AAPL',
)
otp.run(data, date=otp.dt(2024, 2, 1))
```

When some databases in the list lack a tick type, `tick_type` fills them in:

```python
# Equivalent to db=['US_COMP_SAMPLE::TRD', 'CME_SAMPLE::TRD']
data = otp.DataSource(
    db=['US_COMP_SAMPLE', 'CME_SAMPLE'],
    tick_type='TRD',
    symbols='AAPL',
)
```

Parameter `symbols` can be a list.
In this case specified symbols will be merged into a single data flow:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols=['AAPL', 'MSFT'])
>>> data = data[['PRICE']][:6]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
3 2024-02-01 04:00:00.010381671  185.49
4 2024-02-01 04:00:00.011224206  185.50
5 2024-02-01 04:00:00.011671193  185.50
```

Parameter `identify_input_ts` can be used to automatically add field with symbol name for each tick:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD',
...                       symbols=['AAPL', 'MSFT'], identify_input_ts=True)
>>> data = data[['PRICE', 'SYMBOL_NAME', 'TICK_TYPE']][:6]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE SYMBOL_NAME TICK_TYPE
0 2024-02-01 04:00:00.008283417  186.50        AAPL       TRD
1 2024-02-01 04:00:00.008290927  185.59        AAPL       TRD
2 2024-02-01 04:00:00.008291153  185.49        AAPL       TRD
3 2024-02-01 04:00:00.010381671  185.49        AAPL       TRD
4 2024-02-01 04:00:00.011224206  185.50        AAPL       TRD
5 2024-02-01 04:00:00.011671193  185.50        AAPL       TRD
```

``Source`` can also be passed as symbols,
in this case column *SYMBOL_NAME* will be transformed to symbol names
and all other columns will be symbol parameters:

```
>>> symbols = otp.Ticks(SYMBOL_NAME=['AAPL', 'MSFT'])
>>> data = otp.DataSource(db='US_COMP_SAMPLE', symbols=symbols, tick_type='TRD')
>>> data = data[['PRICE']][:6]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE
0 2024-02-01 04:00:00.008283417  186.50
1 2024-02-01 04:00:00.008290927  185.59
2 2024-02-01 04:00:00.008291153  185.49
3 2024-02-01 04:00:00.010381671  185.49
4 2024-02-01 04:00:00.011224206  185.50
5 2024-02-01 04:00:00.011671193  185.50
```

Use `date` to query a full day of data. It sets `start` to the beginning
of the day and `end` to the beginning of the next day (non-inclusive):

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1))
>>> data = data[['PRICE', 'SIZE']][:3]
>>> otp.run(data)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.008283417  186.50     6
1 2024-02-01 04:00:00.008290927  185.59     1
2 2024-02-01 04:00:00.008291153  185.49   107
```

Alternatively, use `start` and `end` for a custom time interval.
Standard ``datetime.datetime`` objects are also accepted:

```python
import datetime
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL',
    start=datetime.datetime(2024, 2, 1, 9, 30),
    end=datetime.datetime(2024, 2, 1, 16, 0),
)
otp.run(data)
```

Or using ``otp.datetime``:

```python
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL',
    start=otp.dt(2024, 2, 1, 9, 30),
    end=otp.dt(2024, 2, 1, 16, 0),
)
```

If `date` is set together with `start`/`end`, `date` takes precedence.

`tick_type` can be a list to merge data from multiple tick types:

```python
# Merge trades and quotes from the same database
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type=['TRD', 'QTE'],
    symbols='AAPL', identify_input_ts=True,
)
# Use identify_input_ts=True to tell which tick type each row came from
```

**tolerant** schema policy checks compatibility with parameter `schema`:

```
>>> data = otp.DataSource(
...     db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', schema={'PRICE': int},
...     schema_policy='tolerant', date=otp.dt(2024, 2, 1),
... )
Traceback (most recent call last):
  ...
ValueError: Database(-s) US_COMP_SAMPLE::TRD schema field PRICE has type <class 'float'>,
but <class 'int'> was requested
```

Schema policy **fail** raises an exception if the schema cannot be deduced:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2021, 3, 1),
...                       schema_policy='fail')  
Traceback (most recent call last):
  ...
ValueError: No ticks found in database(-s) US_COMP_SAMPLE::TRD
```

Schema policy **tolerant_strict** uses `schema` if provided, otherwise falls back to
the database schema. It still validates type compatibility:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL',
...                       schema={'PRICE': float}, date=otp.dt(2024, 2, 1),
...                       schema_policy='tolerant_strict')
>>> data.schema
{'PRICE': <class 'float'>}
```

Schema policy **fail_strict** is like `tolerant_strict` but raises an exception
when the database schema cannot be determined.

The `schema` parameter accepts various types. Use `None` for columns
whose type is irrelevant:

```python
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL',
    schema={
        'PRICE': float,              # 64-bit float
        'SIZE': int,                  # 64-bit integer
        'EXCHANGE': str,              # string (default length 64)
        'COND': otp.string[4],        # fixed-length string of 4 chars
        'MEMO': otp.varstring[256],   # variable-length string up to 256 chars
        'TRADE_TIME': otp.nsectime,   # nanosecond-precision timestamp
        'OTHER': None,                # type will be inferred from database
    },
)
```

`back_to_first_tick` sets how far back to go looking for the latest tick before `start` time:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1),
...                       back_to_first_tick=otp.Day(1))
>>> data = data[['PRICE', 'SIZE']][:4]
>>> otp.run(data)
                           Time   PRICE  SIZE
0 2024-02-01 00:00:00.000000000  185.68    10
1 2024-02-01 04:00:00.008283417  186.50     6
2 2024-02-01 04:00:00.008290927  185.59     1
3 2024-02-01 04:00:00.008291153  185.49   107
```

`keep_first_tick_timestamp` allows to show the original timestamp of the tick that was taken from before
the start time of the query:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1),
...                       back_to_first_tick=otp.Day(1), keep_first_tick_timestamp='ORIGIN_TIMESTAMP')
>>> data = data[['PRICE', 'SIZE', 'ORIGIN_TIMESTAMP']][:4]
>>> otp.run(data)
                           Time   PRICE  SIZE              ORIGIN_TIMESTAMP
0 2024-02-01 00:00:00.000000000  185.68    10 2024-01-31 19:59:57.877747563
1 2024-02-01 04:00:00.008283417  186.50     6 2024-02-01 04:00:00.008283417
2 2024-02-01 04:00:00.008290927  185.59     1 2024-02-01 04:00:00.008290927
3 2024-02-01 04:00:00.008291153  185.49   107 2024-02-01 04:00:00.008291153
```

`max_back_ticks_to_prepend` is used with `back_to_first_tick`
if more than 1 ticks before start time should be retrieved:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1),
...                       max_back_ticks_to_prepend=2, back_to_first_tick=otp.Day(1),
...                       keep_first_tick_timestamp='ORIGIN_TIMESTAMP')
>>> data = data[['PRICE', 'SIZE', 'ORIGIN_TIMESTAMP']][:5]
>>> otp.run(data)
                           Time     PRICE  SIZE              ORIGIN_TIMESTAMP
0 2024-02-01 00:00:00.000000000  185.6999     5 2024-01-31 19:59:53.314804162
1 2024-02-01 00:00:00.000000000  185.6800    10 2024-01-31 19:59:57.877747563
2 2024-02-01 04:00:00.008283417  186.5000     6 2024-02-01 04:00:00.008283417
3 2024-02-01 04:00:00.008290927  185.5900     1 2024-02-01 04:00:00.008290927
4 2024-02-01 04:00:00.008291153  185.4900   107 2024-02-01 04:00:00.008291153
```

`where_clause_for_back_ticks` is used to filter out ticks before the start time:

```python
data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1),
                      where_clause_for_back_ticks=otp.raw('SIZE>=50', dtype=bool),
                      back_to_first_tick=otp.Day(1), max_back_ticks_to_prepend=2,
                      keep_first_tick_timestamp='ORIGIN_TIMESTAMP')
data = data[['PRICE', 'SIZE', 'ORIGIN_TIMESTAMP']][:5]
df = otp.run(data)
print(df)
```

```
                           Time   PRICE  SIZE              ORIGIN_TIMESTAMP
0 2024-02-01 00:00:00.000000000  185.70   440 2024-01-31 19:59:51.062739337
1 2024-02-01 00:00:00.000000000  185.70   100 2024-01-31 19:59:53.302285347
2 2024-02-01 04:00:00.008283417  186.50     6 2024-02-01 04:00:00.008283417
3 2024-02-01 04:00:00.008290927  185.59     1 2024-02-01 04:00:00.008290927
4 2024-02-01 04:00:00.008291153  185.49   107 2024-02-01 04:00:00.008291153
```

`presort` controls whether to use parallel data fetching when querying multiple
bound symbols. It defaults to True when `symbols` is set. Set to False to use
sequential MERGE instead (useful for debugging or when order must be strictly preserved):

```python
# With presort (default) - parallel fetching, faster for many symbols
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD',
    symbols=['AAPL', 'MSFT', 'GOOGL'],
    presort=True,  # this is the default when symbols is set
)

# Without presort - sequential merge
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD',
    symbols=['AAPL', 'MSFT', 'GOOGL'],
    presort=False,
)
```

`batch_size` and `concurrency` tune presort performance.
`batch_size` controls how many symbols are processed per batch,
and `concurrency` sets the number of CPU cores:

```python
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD',
    symbols=large_symbol_list,  # e.g., 500+ symbols
    batch_size=50,              # process 50 symbols at a time
    concurrency=4,              # use 4 CPU cores
)
```

`symbol_date` specifies the date for resolving symbols in date-dependent symbologies.
It is only applicable when `symbols` is set:

```python
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD',
    symbols=['AAPL', 'MSFT'],
    symbol_date=otp.dt(2024, 2, 1),
)

# symbol_date also accepts integers in YYYYMMDD format
data = otp.DataSource(
    db='US_COMP_SAMPLE', tick_type='TRD',
    symbols=['AAPL', 'MSFT'],
    symbol_date=20240201,
)
```

Parameter `db` can also be an ``DB`` object:

```python
db = otp.DB('US_COMP_SAMPLE')
data = otp.DataSource(db=db, tick_type='TRD', symbols='AAPL')
otp.run(data, date=otp.dt(2024, 2, 1))
```

##### SEE ALSO
`Query start / end flow`
`Symbols: bound and unbound`
