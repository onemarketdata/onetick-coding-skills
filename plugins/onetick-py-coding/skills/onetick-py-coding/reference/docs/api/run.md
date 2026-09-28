# otp.run

### ``run(query, *, symbols=None, start=utils.adaptive, end=utils.adaptive, date=None, start_time_expression=None, end_time_expression=None, timezone=utils.default, context=utils.default, username=None, alternative_username=None, password=None, batch_size=utils.default, running=None, query_properties=None, concurrency=utils.default, apply_times_daily=None, symbol_date=None, query_params=None, time_as_nsec=True, treat_byte_arrays_as_strings=True, output_matrix_per_field=False, output_structure=None, return_utc_times=None, connection=None, callback=None, svg_path=None, use_connection_pool=False, node_name=None, require_dict=False, max_expected_ticks_per_symbol=None, log_symbol=utils.default, encoding=None, manual_dataframe_callback=False, print_symbol_errors=utils.default, preserve_decimal_flag=None)``

Executes a query and returns its result.

* **Parameters:**
  * **query** (``onetick.py.Source``, otq.Ep, otq.GraphQuery, otq.ChainQuery, str, otq.Chainlet,            Callable, otq.SqlQuery, ``onetick.py.SqlQuery``) – 

    Query to execute can be source, path of the query on a disk or onetick.query graph or event processor.
    For running OTQ files, it represents the path (including filename) to the OTQ file to run a single query within
    the file. If more than one query is present, then the query to be run must be specified
    (that is, `'path_to_file/otq_file.otq::query_to_run'`).

    `query` can also be a function that has a symbol object as the first parameter.
    This object can be used to get symbol name and symbol parameters.
    Function must return a ``Source``.
  * **symbols** (str, list of str, list of otq.Symbol, ``onetick.py.Source``, `pandas.DataFrame`,             ``otp.eval``, optional) – Symbol(s) to run the query for passed as a string, a list of strings,
    a `pandas.DataFrame` with the `SYMBOL_NAME` column,
    or as a “symbols” query which results include the `SYMBOL_NAME` column.
    The start/end times for the symbols query will taken from the params below.
    See `symbols` for more details.
  * **start** (``datetime.datetime``, ``otp.datetime``, optional) – The start time of the query. Can be timezone-naive or timezone-aware. See also `timezone` argument.
    onetick.py uses ``otp.config.default_start_time``
    as default value, if you don’t want to specify start time, e.g. to use saved time of the query,
    then you should specify None value.
  * **end** (``datetime.datetime``, ``otp.datetime``, optional) – The end time of the query (note that it’s non-inclusive).
    Can be timezone-naive or timezone-aware. See also `timezone` argument.
    onetick.py uses ``otp.config.default_end_time``
    as default value, if you don’t want to specify end time, e.g. to use saved time of the query,
    then you should specify None value.
  * **date** (``datetime.date``, ``otp.date``, optional) – The date to run the query for. Can be set instead of `start` and `end` parameters.
    If set then the interval to run the query will be from 0:00 to 24:00 of the specified date.
  * **start_time_expression** (str, ``Operation``, optional) – Start time onetick expression of the query. If specified, it will take precedence over `start`.
    Supported only if query is Source, Graph or Event Processor.
  * **end_time_expression** (str, ``Operation``, optional) – End time onetick expression of the query. If specified, it will take precedence over `end`.
    Supported only if query is Source, Graph or Event Processor.
  * **timezone** (*str* *,* *optional*) – The timezone of output timestamps.
    Also, when start and/or end arguments are timezone-naive, it will define their timezone.
    If parameter is omitted timestamps of ticks will be formatted
    with the default ``otp.config.tz``.
  * **context** (*str* *,* *optional*) – Allows specification of different contexts from OneTick configuration to connect to.
    If not set then default ``otp.config.context`` is used.
    See `guide about switching contexts` for examples.
  * **username** (*str* *|* *None*) – The username to make the connection.
    By default the user which executed the process is used or the value specified in
    ``otp.config.default_username``.
  * **alternative_username** (*str*) – The username used for authentication.
    Needs to be set only when the tick server is configured to use password-based authentication.
    By default,
    ``otp.config.default_auth_username`` is used.
    Not supported for WebAPI mode.
  * **password** (*str* *,* *optional*) – The password used for authentication.
    Needs to be set only when the tick server is configured to use password-based authentication.
    By default, ``otp.config.default_password`` is used.
    Note: not supported and ignored on older OneTick versions.
    Not supported for WebAPI mode.
  * **batch_size** (*int*) – Number of symbols to process in one batch. Larger batch sizes reduce overhead
    but use more memory.
    By default, the value from
    ``otp.config.default_batch_size`` is used.
    Not supported for WebAPI mode.
  * **running** (*bool* *,* *optional*) – Set to True for CEP (Complex Event Processing) real-time streaming queries.
    Default is False.
  * **query_properties** (*dict* *,* *optional*) – Query properties, such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
  * **concurrency** (*int* *,* *optional*) – The maximum number of CPU cores to use to process the query.
    By default, the value from
    ``otp.config.default_concurrency`` is used.
  * **apply_times_daily** (*bool*) – 

    Runs the query for every day in the `start`-`end` time range,
    using the time components of `start` and `end` datetimes.

    Note that those daily intervals are executed separately, so you don’t have access
    to the data from previous or next days (see example in the next section).
  * **symbol_date** (``datetime.datetime``, int, str, optional) – Date used for resolving symbols in date-dependent symbologies, where the same
    identifier can map to different instruments on different dates.
    Accepts a datetime object or integer in `YYYYMMDD` format (e.g., `20220301`).
  * **query_params** (*dict*) – Parameters of the query.
  * **time_as_nsec** (*bool*) – If True, output timestamps have nanosecond granularity.
    If False, timestamps are truncated to microsecond granularity.
    Default is True.
  * **treat_byte_arrays_as_strings** (*bool*) – Outputs byte arrays as strings (defaults to True)
  * **output_matrix_per_field** (*bool*) – Changes output format to list of matrices per field.
    Not supported for WebAPI mode.
  * **output_structure** (*otp.Source.OutputStructure* *,* *optional*) – 

    Structure (type) of the result. Supported values are:
    : - df (default) - the result is returned as `pandas.DataFrame` object
        or dictionary of symbol names and `pandas.DataFrame` objects
        in case of using multiple symbols or first stage query.
      - map - the result is returned as SymbolNumpyResultMap.
      - list - the result is returned as list.
      - polars - the result is returned as
        `polars.DataFrame` object
        or dictionary of symbol names and dataframe objects
        (**Only supported in WebAPI mode**).
      - pandas - the result is returned as `pandas.DataFrame`
        (**Only supported in WebAPI mode**).
      - pyarrow - the result is returned as `pyarrow.Table`
        (**Only supported in WebAPI mode**).

    df output structure is default for both standard and WebAPI modes.
    In this mode onetick-py converts numpy data structure returned from onetick.query to pandas.Dataframe.

    pandas output structure (available only in WebAPI mode), instead, returns pandas.Dataframe
    directly from OneTick.
  * **return_utc_times** (*bool*) – If True, return timestamps in UTC timezone. If False, return in local timezone.
    Not supported for WebAPI mode.
  * **connection** (`pyomd.Connection`) – The connection to be used for discovering nested .otq files
    Not supported for WebAPI mode.
  * **callback** (``onetick.py.CallbackBase``) – Class with callback methods.
    If set, the output of the query should be controlled with callbacks
    and this function returns nothing.
  * **svg_path** (*str* *,* *optional*) – Path to render graph in case `otq.API_CONFIG['RENDER_GRAPH_ON_ERROR']` is set.
  * **use_connection_pool** (*bool*) – Default is False. If set to True, the connection pool is used.
    Not supported for WebAPI mode.
  * **node_name** (*str* *,* *list* **[*str* *]* *,* *optional*) – Name of the output node to select result from. If query graph has several output nodes, you can specify the name
    of the node to choose result from. If node_name was specified, query should be presented by path on the disk
    and output_structure should be df
  * **require_dict** (*bool*) – If True, the result is always returned as a dictionary keyed by symbol name,
    even when only a single symbol is queried. Default is False.
  * **max_expected_ticks_per_symbol** (*int*) – Expected maximum number of ticks per symbol (used for performance optimizations).
    By default, ``otp.config.max_expected_ticks_per_symbol`` is used.
    Not supported for WebAPI mode.
  * **log_symbol** (*bool*) – Log currently executed symbol.
    Note that this only works with unbound symbols.
    Also in this case ``otp.run`` is executed in `callback` mode
    and no value is returned from the function, so it should be used only for debugging purposes.
    This logging will not work if some other value specified in parameter `callback`.
    By default, ``otp.config.log_symbol`` is used.
  * **encoding** (*str* *,* *optional*) – The encoding of string fields.
  * **manual_dataframe_callback** (*bool*) – Create dataframe manually with `callback` mode.
    Only works if `output_structure='df'` is specified and parameter `callback` is not.
    May improve performance in some cases.
  * **print_symbol_errors** (*bool*) – If True (default), symbol-level errors from OneTick are printed as Python warnings.
    Applicable only when `output_structure` is `'df'`.
    By default, ``otp.config.print_symbol_errors``
    is used, which is True by default.
  * **preserve_decimal_flag** (*bool*) – The flag controls how decimal type columns are returned from this function.
    If set to False (default), they are returned as float values, with possible precision loss.
    If set to True, they are returned as ``decimal.Decimal`` objects without precision loss.
    This parameter may not be supported on older OneTick versions.
* **Returns:**
  result of the query
* **Return type:**
  result, list, dict, `pandas.DataFrame`, None

##### Examples

Running ``onetick.py.Source`` and setting start and end times:

```
>>> data = otp.Tick(A=1)
>>> otp.run(data, start=otp.dt(2003, 12, 2), end=otp.dt(2003, 12, 4))
        Time  A
0 2003-12-02  1
```

Setting query interval with `date` parameter:

```
>>> data = otp.Tick(A=1)
>>> data['START'] = data['_START_TIME']
>>> data['END'] = data['_END_TIME']
>>> otp.run(data, date=otp.dt(2003, 12, 1))
        Time  A      START        END
0 2003-12-01  1 2003-12-01 2003-12-02
```

Running otq.Ep and passing query parameters:

```
>>> ep = otq.TickGenerator(bucket_interval=0, fields='long A = $X').tick_type('TT')
>>> otp.run(ep, symbols='LOCAL::', query_params={'X': 1})
        Time  A
0 2003-12-04  1
```

Running in callback mode:

```
>>> class Callback(otp.CallbackBase):
...     def __init__(self):
...         self.result = None
...     def process_tick(self, tick, time):
...         self.result = tick
>>> data = otp.Tick(A=1)
>>> callback = Callback()
>>> otp.run(data, callback=callback)
>>> callback.result
{'A': 1}
```

Running with `apply_times_daily`.
Note that daily intervals are processed separately so, for example,
we can’t access column **COUNT** from previous day.

```
>>> trd = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> trd = trd.agg({'COUNT': otp.agg.count()},
...               bucket_interval=12 * 3600, bucket_time='start')
>>> trd['PREV_COUNT'] = trd['COUNT'][-1]
>>> otp.run(trd, apply_times_daily=True,
...         start=otp.dt(2024, 2, 1), end=otp.dt(2024, 2, 3))
                 Time   COUNT  PREV_COUNT
0 2024-02-01 00:00:00  392770           0
1 2024-02-01 12:00:00  428211      392770
2 2024-02-02 00:00:00  750682           0
3 2024-02-02 12:00:00  357788      750682
```

Using a function as a `query`, accessing symbol name and parameters:

```
>>> def query(symbol):
...     t = otp.Tick(X='x')
...     t['SYMBOL_NAME'] = symbol.name
...     t['SYMBOL_PARAM'] = symbol.PARAM
...     return t
>>> symbols = otp.Ticks({'SYMBOL_NAME': ['A', 'B'], 'PARAM': [1, 2]})
>>> result = otp.run(query, symbols=symbols)
>>> result['A']
        Time  X SYMBOL_NAME  SYMBOL_PARAM
0 2003-12-01  x           A             1
>>> result['B']
        Time  X SYMBOL_NAME  SYMBOL_PARAM
0 2003-12-01  x           B             2
```

Debugging unbound symbols with `log_symbol` parameter:

```
>>> data = otp.Tick(X=1)
>>> symbols = otp.Ticks({'SYMBOL_NAME': ['A', 'B'], 'PARAM': [1, 2]})
>>> otp.run(query, symbols=symbols, log_symbol=True)  
Running query <onetick.py.sources.ticks.Tick object at ...>
Processing symbol A
Processing symbol B
```

By default, some non-standard characters in data strings could be processed incorrectly:

```
>>> data = ['AA測試AA']
>>> source = otp.Ticks({'A': data})
>>> otp.run(source)
        Time           A
0 2003-12-01  AAæ¸¬è©¦AA
```

To fix this you can pass `encoding` parameter:

```
>>> data = ['AA測試AA']
>>> source = otp.Ticks({'A': data})
>>> otp.run(source, encoding="utf-8")
        Time        A
0 2003-12-01  AA測試AA
```

Note that query `start` time is inclusive, but query `end` time is not,
meaning that ticks with timestamps equal to the query end time will not be included:

```
>>> data = otp.Tick(A=1, bucket_interval=24*60*60)
>>> data['A'] = data['TIMESTAMP'].dt.day_of_month()
>>> otp.run(data, start=otp.dt(2003, 12, 1), end=otp.dt(2003, 12, 4))
        Time  A
0 2003-12-01  1
1 2003-12-02  2
2 2003-12-03  3
>>> otp.run(data, start=otp.dt(2003, 12, 1), end=otp.dt(2003, 12, 2))
        Time  A
0 2003-12-01  1
```

If you want to include such ticks, you can add one nanosecond to the query end time:

```
>>> otp.run(data, start=otp.dt(2003, 12, 1), end=otp.dt(2003, 12, 2) + otp.Nano(1))
        Time  A
0 2003-12-01  1
1 2003-12-02  2
```

Using pandas.DataFrame as a symbol list:

```
>>> symbols_df = pd.DataFrame({'SYMBOL_NAME': ['AAPL', 'MSFT'], 'SYMBOL_PARAM': ['a', 'b']})
>>> data = otp.Tick(A=1)
>>> data['SYMBOL_NAME'] = data.Symbol.name
>>> data['SYMBOL_PARAM'] = data.Symbol.get('SYMBOL_PARAM', otp.string[64])
>>> result = otp.run(data, symbols=symbols_df)
>>> result['AAPL']
        Time  A SYMBOL_NAME SYMBOL_PARAM
0 2003-12-01  1        AAPL            a
>>> result['MSFT']
        Time  A SYMBOL_NAME SYMBOL_PARAM
0 2003-12-01  1        MSFT            b
```

Setting `timezone` controls the output timestamp timezone.
When `start`/`end` are timezone-naive, it also defines their timezone:

```
>>> data = otp.Tick(A=1)
>>> otp.run(data, start=otp.dt(2003, 12, 1), end=otp.dt(2003, 12, 2), timezone='EST5EDT')
        Time  A
0 2003-12-01  1
```

Running for multiple symbols returns a dictionary keyed by symbol name:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD',
...                       schema_policy='manual_strict', schema={'PRICE': float, 'SIZE': float})
>>> data = data[:3]
>>> result = otp.run(data, symbols=['AAPL', 'MSFT'], date=otp.dt(2024, 2, 1))
>>> result['AAPL']
                           Time   PRICE   SIZE
0 2024-02-01 04:00:00.008283417  186.50    6.0
1 2024-02-01 04:00:00.008290927  185.59    1.0
2 2024-02-01 04:00:00.008291153  185.49  107.0
>>> result['MSFT']
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.016997102  400.15  42.0
1 2024-02-01 04:00:00.024299525  402.00  30.0
2 2024-02-01 04:00:00.024325756  402.00  10.0
```

Use `require_dict=True` to always get a dictionary result,
even when running a single symbol:

```
>>> data = otp.Tick(A=1)
>>> result = otp.run(data, require_dict=True)
>>> type(result)
<class 'dict'>
```

Using a ``Source`` as `symbols` creates a first-stage query
that dynamically generates the symbol list. The source must produce a `SYMBOL_NAME` column:

```python
# First-stage query: get symbols from the database
symbol_src = otp.Symbols('US_COMP_SAMPLE', for_tick_type='TRD')[:3]

data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')[:1]
result = otp.run(data, symbols=symbol_src, date=otp.dt(2024, 2, 1))
# result is a dict keyed by symbol names from symbol_src
```

`output_structure` controls the format of the return value.
Use `'list'` to get raw results as a list of tuples:

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL', date=otp.dt(2024, 2, 1))
result = otp.run(data, output_structure='list')
# result is [(symbol, ticks_data, error_data, node_name), ...]
```

Use `output_structure='map'` for a `SymbolNumpyResultMap` object:

```python
result = otp.run(data, output_structure='map')
```

`running=True` marks the query as a CEP (Complex Event Processing) query
for real-time streaming:

```python
# CEP query for real-time data
data = otp.DataSource('US_COMP_REPLAY', tick_type='TRD', symbols='AAPL')
result = otp.run(data, running=True, start=otp.now(), end=otp.now() + otp.Second(3))
```

`batch_size` and `concurrency` tune performance for multi-symbol queries:

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
result = otp.run(data,
                 symbols=large_symbol_list,
                 batch_size=50,    # process 50 symbols per batch
                 concurrency=4)    # use 4 CPU cores
```

`symbol_date` specifies the date for resolving symbols
in date-dependent symbologies:

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
result = otp.run(data,
                 symbols=['AAPL', 'MSFT'],
                 symbol_date=otp.dt(2024, 2, 1),
                 date=otp.dt(2024, 2, 1))

# Also accepts integer YYYYMMDD format
result = otp.run(data, symbols=['AAPL'], symbol_date=20240201,
                 date=otp.dt(2024, 2, 1))
```

`start_time_expression` and `end_time_expression` allow dynamic time boundaries
using OneTick expressions. They take precedence over `start`/`end`:

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
result = otp.run(data, symbols='AAPL',
                 start_time_expression='20240201093000',
                 end_time_expression='20240201160000')
```

`query_properties` passes OneTick query properties as a dict:

```python
data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
result = otp.run(data, symbols='AAPL',
                 query_properties={'ALLOW_GRAPH_REUSE': 'true'},
                 date=otp.dt(2024, 2, 1))
```

`node_name` selects the output from a specific node when running
an OTQ file with multiple output nodes:

```python
result = otp.run('path/to/multi_output.otq',
                 symbols='AAPL', node_name='OUTPUT_1',
                 date=otp.dt(2022, 3, 1))
```
