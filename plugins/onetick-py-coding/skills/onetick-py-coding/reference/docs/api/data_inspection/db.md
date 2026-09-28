# otp.inspection.DB

### ``class DB(name, description='', context=utils.default)``

Bases: ``object``

An object of available databases that the ``otp.databases()`` function returns.
It helps to make initial analysis on the database level: available tick types,
dates with data, symbols, tick schema, etc.

#### ``access_info(deep_scan=False, username=None, query_properties=None)``

Get access info for this database and `username`.

All dates are returned in GMT timezone.

* **Parameters:**
  * **deep_scan** – 

    If False (default) then the access fields are returned from the configuration of the database
    (basically the same fields as specified in the locator) and the dictionary is returned.
    If True then access fields are returned for each available remote host and time interval
    and the `pandas.DataFrame` object is returned.

    #### WARNING
    Setting this flag may introduce significant delay for query execution
    in some OneTick configurations and environments,
    because it has to check all accessible remote servers.
  * **username** – Can be used to specify the user for which the query will be executed.
    By default the query is executed for the current user.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Return type:**
  *DataFrame* | `dict`

##### Examples

By default access fields from the basic configuration of the database are returned:

```
>>> db = otp.databases()['US_COMP_SAMPLE']  
>>> db.access_info()                        
{'DB_NAME': 'US_COMP_SAMPLE',
 'READ_ACCESS': 1,
 'WRITE_ACCESS': 0,
 'MIN_AGE_SET': 0,
 'MIN_AGE_MSEC': 0,
 'MAX_AGE_SET': 0,
 'MAX_AGE_MSEC': 0,
 'MIN_START_DATE_SET': 0,
 'MIN_START_DATE_MSEC': Timestamp('1970-01-01 00:00:00'),
 'MAX_END_DATE_SET': 0,
 'MAX_END_DATE_MSEC': Timestamp('1970-01-01 00:00:00'),
 'MAX_QUERY_DURATION_SET': 0,
 'MAX_QUERY_DURATION': '',
 'MIN_AGE_DB_DAYS': 0,
 'MIN_AGE_DB_DAYS_SET': 0,
 'MAX_AGE_DB_DAYS': 0,
 'MAX_AGE_DB_DAYS_SET': 0,
 'CEP_ACCESS': 1,
 'DESTROY_ACCESS': 0,
 'MEMDB_ACCESS': 1}
```

Set parameter `deep_scan` to True to return access fields from each available host and time interval:

```
>>> db.access_info(deep_scan=True)  
          DB_NAME  READ_ACCESS  WRITE_ACCESS  MIN_AGE_SET  MIN_AGE_MSEC  MAX_AGE_SET  ...                       CEP_ACCESS  DESTROY_ACCESS MEMDB_ACCESS         SERVER_ADDRESS INTERVAL_START  INTERVAL_END
0  US_COMP_SAMPLE            1             0            0             0            0  ...                                1               0            1                    ...     2024-01-01    2038-01-01
```

##### SEE ALSO
**ACCESS_INFO** OneTick event processor

#### ``show_config(config_type='locator_entry', query_properties=None)``

Shows the specified configuration for a database.

* **Parameters:**
  * **config_type** (*str*) – 

    If **‘locator_entry’** is specified, a string representing db’s locator entry along with VDB_FLAG
    (this flag equals 1 when the database is virtual and 0 otherwise) will be returned.

    If **‘db_time_intervals’** is specified,
    then time intervals configured in the locator file will be propagated
    including additional information, such as
    LOCATION, ARCHIVE_DURATION, DAY_BOUNDARY_TZ, DAY_BOUNDARY_OFFSET, ALTERNATIVE_LOCATIONS, etc.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Return type:**
  `dict`

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> print(db.show_config()['LOCATOR_STRING'])  
<DB ARCHIVE_COMPRESSION_TYPE="NATIVE_PLUS_GZIP" ... DAY_BOUNDARY_TZ="EST5EDT" ... ID="US_COMP_SAMPLE" ...>
<LOCATIONS >
    <LOCATION ACCESS_METHOD="file" END_TIME="20380101000000" LOCATION="..." ... />
</LOCATIONS>
<RAW_DATA />
</DB>
```

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.show_config(config_type='db_time_intervals')  
{'START_DATE': 1704067200000,
 'END_DATE': 2145916800000,
 'GROWABLE_ARCHIVE_FLAG': 0,
 'ARCHIVE_DURATION': 0,
 'LOCATION': '...',
 'DAY_BOUNDARY_TZ': 'EST5EDT',
 'DAY_BOUNDARY_OFFSET': 0,
 'ALTERNATIVE_LOCATIONS': ''}
```

##### SEE ALSO
**DB/SHOW_CONFIG** OneTick event processor

#### ``property min_acl_start_date *: `date` | `None`*``

Minimum start date set in ACL for current user.
Returns None if not set.

#### ``property max_acl_end_date *: `date` | `None`*``

Maximum end date set in ACL for current user.
Returns None if not set.

#### ``dates(respect_acl=False, check_index_file=utils.adaptive)``

Returns list of dates in GMT timezone for which data is available.

* **Parameters:**
  * **respect_acl** (*bool*) – If True then only the dates that current user has access to will be returned
  * **check_index_file** (*bool*) – If True, then file *index* will be searched for to determine if a database is loaded for a date.
    This check may be expensive, in terms of time it takes,
    when the file resides on NFS or on object storage, such as S3.
    If this parameter is set to False, then only the database directory for a date will be searched.
    This will increase performance, but may also return the days that are configured
    but where there is actually no data.
    By default this option is set to False if it is supported by API and the server,
    otherwise it is set to True.
* **Returns:**
  Returns `None` when there is no data in the database
* **Return type:**
  `datetime.date` or `None`

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.dates()  
[datetime.date(2024, 1, 2), ..., datetime.date(2024, 3, 28)]
```

#### ``property last_date``

The latest date in GMT timezone on which db has data and the current user has access to.

* **Returns:**
  Returns `None` when there is no data in the database
* **Return type:**
  `datetime.date` or `None`

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.last_date
datetime.date(2024, 3, 28)
```

#### ``tick_types(date=None, timezone=None, query_properties=None, include_memdb=True)``

Returns list of tick types for the `date`.

* **Parameters:**
  * **date** (``otp.dt``, ``datetime.datetime``, optional) – Date for the tick types look up. `None` means the ``last_date``
  * **timezone** (*str* *,* *optional*) – Timezone for the look up. `None` means the default timezone.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
  * **include_memdb** (*bool*) – Setting this parameter to True will return result from memory databases too.
    Otherwise only the archive databases will be used.
    Default is True.
* **Returns:**
  List with string values of available tick types.
* **Return type:**
  `list`

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.tick_types(date=otp.dt(2024, 2, 1))
['DAY', 'IND', 'LULD', 'MKT', 'NBBO', 'QTE', 'STAT', 'TRD']
```

#### ``schema(date=None, tick_type=None, timezone=None, check_index_file=utils.adaptive, query_properties=None, include_memdb=True)``

Gets the schema of the database.

* **Parameters:**
  * **date** (``otp.dt``, ``datetime.datetime``, optional) – Date for the schema. `None` means the ``last_date``
  * **tick_type** (*str* *,* *optional*) – Specifies a tick type for schema. `None` means use the one available
    tick type, if there are multiple tick types then it raises the `Exception`.
    It uses the ``tick_types()`` method.
  * **timezone** (*str* *,* *optional*) – Allows to specify a timezone for searching tick types.
  * **check_index_file** (*bool*) – If True, then file *index* will be searched for to determine if a database is loaded for a date.
    This check may be expensive, in terms of time it takes,
    when the file resides on NFS or on object storage, such as S3.
    If this parameter is set to False, then only the database directory for a date will be searched.
    This will increase performance, but may also return the days that are configured
    but where there is actually no data.
    By default this option is set to False if it is supported by API and the server,
    otherwise it is set to True.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
  * **include_memdb** (*bool*) – Setting this parameter to True will return result from memory databases too.
    Otherwise only the archive databases will be used.
    Default is True.
* **Returns:**
  Dict where keys are field names and values are `onetick.py` `types`.
  It’s compatible with the ``onetick.py.Source.schema`` methods.
* **Return type:**
  `dict`

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.schema(tick_type='TRD', date=otp.dt(2024, 2, 1))
{'COND': string[4],
 'CORR': <class 'onetick.py.types._int'>,
 'DELETED_TIME': <class 'onetick.py.types.msectime'>,
 'EXCHANGE': string[1],
 'OMDSEQ': <class 'onetick.py.types.uint'>,
 'PARTICIPANT_TIME': <class 'onetick.py.types.nsectime'>,
 'PRICE': <class 'float'>, 'SEQ_NUM': <class 'int'>,
 'SIZE': <class 'int'>, 'SOURCE': string[1],
 'STOP_STOCK': string[1],
 'TICKER': string[16],
 'TICK_STATUS': <class 'onetick.py.types._int'>,
 'TRADE_ID': string[20],
 'TRF': string[1],
 'TRF_TIME': <class 'onetick.py.types.nsectime'>,
 'TTE': string[1]}
```

#### ``symbols(date=None, timezone=None, tick_type=None, pattern='.*', query_properties=None, include_memdb=True)``

Finds a list of available symbols in the database

* **Parameters:**
  * **date** (``otp.dt``, ``datetime.datetime``, optional) – Date for the symbols look up. `None` means the ``last_date``
  * **tick_type** (*str* *,* *optional*) – Tick type for symbols. `None` means union across all tick types.
  * **timezone** (*str* *,* *optional*) – Timezone for the lookup. `None` means the default timezone.
  * **pattern** (*str*) – Regular expression to select symbols.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
  * **include_memdb** (*bool*) – Setting this parameter to True will return result from memory databases too.
    Otherwise only the archive databases will be used.
    Default is True.
* **Return type:**
  `list``[str`]

##### Examples

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.symbols(date=otp.dt(2024, 2, 1), tick_type='TRD', pattern='^AA.*')
['AAL', 'AAPL']
```

#### ``show_archive_stats(start=utils.adaptive, end=utils.adaptive, date=None, timezone='GMT', query_properties=None)``

This method shows various stats about the queried symbol,
as well as an archive as a whole for each day within the queried interval.

Accelerator databases are not supported.
Memory databases will be ignored even within their life hours.

Archive stats returned:

> * COMPRESSION_TYPE - archive compression type.
>   In older archives native compression flag is not stored,
>   so for example for gzip compression this field may say “GZIP or NATIVE_PLUS_GZIP”.
>   The meta_data_upgrader.exe tool can be used to determine and inject that information in such cases
>   in order to get a more precise result in this field.
> * TIME_RANGE_VALIDITY - whether lowest and highest loaded timestamps (see below) are known.
>   Like native compression flag, this information is missing in older archives
>   and can be added using meta_data_upgrader.exe tool.
> * LOWEST_LOADED_DATETIME - the lowest loaded timestamp for the queried interval (across all symbols)
> * HIGHEST_LOADED_DATETIME - the highest loaded timestamp for the queried interval (across all symbols)
> * TOTAL_TICKS - the number of ticks for the queried interval (across all symbols).
>   Also missing in older archives and can be added using meta_data_upgrader.exe.
>   If not available, -1 will be returned.
> * SYMBOL_DATA_SIZE - the size of the symbol in archive in bytes.
>   This information is also missing in older archives, however the other options, it cannot later be added.
>   In such cases -1 will be returned.
> * TOTAL_SYMBOLS - the number of symbols for the queried interval
> * TOTAL_SIZE - archive size in bytes for the queried interval
>   (including the garbage potentially accumulated during appends).
* **Parameters:**
  * **start** (``otp.dt``, optional) – Start of the query time range.
  * **end** (``otp.dt``, optional) – End of the query time range.
  * **date** (``otp.dt``, optional) – Date to query. Can be set instead of `start` and `end`.
  * **timezone** (*str*) – Timezone for the query. Default is GMT.
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Return type:**
  *DataFrame*

#### NOTE
Fields **LOWEST_LOADED_DATETIME** and **HIGHEST_LOADED_DATETIME** are returned in GMT timezone,
so the default value of parameter `timezone` is GMT too.

##### Examples

Show stats for a particular date for a database *US_COMP_SAMPLE*:

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.show_archive_stats(date=otp.dt(2024, 2, 1))  
                 Time  COMPRESSION_TYPE TIME_RANGE_VALIDITY LOWEST_LOADED_DATETIME HIGHEST_LOADED_DATETIME  ...              TOTAL_TICKS  TOTAL_SYMBOLS   TOTAL_SIZE  ARCHIVE_MODIFICATION_TIME NATIVE_COMPR_HEADERS_SIZE
0 2024-02-01 00:00:00  NATIVE_PLUS_GZIP               VALID    2024-01-31 05:00:00     2024-02-01 01:15:00  ...                777458840           3371  10091704128        2025-11-05 06:09:55                        -1
1 2024-02-01 05:00:00  NATIVE_PLUS_GZIP               VALID    2024-02-01 05:00:00     2024-02-02 01:15:00  ...                826170303           3308  10603315515        2025-11-05 06:08:18                        -1
```

##### SEE ALSO
**SHOW_ARCHIVE_STATS** OneTick event processor

#### ``ref_data(ref_data_type, symbol_date=None, start=utils.adaptive, end=utils.adaptive, date=None, timezone=utils.default, symbol='', query_properties=None)``

Shows reference data for the specified security and reference data type.

It can be used to view corporation actions,
symbol name changes,
primary exchange info and symbology mapping for a securities,
as well as the list of symbologies,
names of custom adjustment types for corporate actions present in a reference database
as well as names of continuous contracts in database symbology.

* **Parameters:**
  * **ref_data_type** (*str*) – 

    Type of reference data to be queried. Possible values are:
    > * corp_actions
    > * symbol_name_history
    > * primary_exchange
    > * symbol_calendar
    > * symbol_currency
    > * symbology_mapping
    > * symbology_list
    > * custom_adjustment_type_list
    > * all_calendars
    > * all_continuous_contract_names
  * **symbol_date** – This parameter must be specified for some reference data types to be queried.
  * **symbol** (*str*) – Symbol name for the query (may be useful for some `ref_data_type`).
  * **query_properties** (*dict* *,* *optional*) – Query properties passed to ``otp.run``,
    such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc.
* **Return type:**
  *DataFrame*

##### Examples

Show calendars for a database US_COMP_SAMPLE in the given range:

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.ref_data('all_calendars',
...             date=otp.dt(2024, 1, 1),
...             symbol='AAPL',
...             timezone='EST5EDT',
...             symbol_date=otp.dt(2024, 1, 1))  
          Time END_DATETIME       CALENDAR_NAME SESSION_NAME SESSION_FLAGS DAY_PATTERN  START_HHMMSS              END_HHMMSS          TIMEZONE  PRIORITY                    DESCRIPTION
0   2024-01-01   2024-01-02  BBG_EQUITY_EXCH_US     DAY_TYPE             R   0.0.12345             0                  240000  America/New_York         0                    @US_DEFAULT
1   2024-01-01   2024-01-02  BBG_EQUITY_EXCH_US   PRE_MARKET             b   0.0.12345         40000                   93000  America/New_York         0                    @US_DEFAULT
2   2024-01-01   2024-01-02  BBG_EQUITY_EXCH_US       MARKET             r   0.0.12345         93000                  160000  America/New_York         0                    @US_DEFAULT
3   2024-01-01   2024-01-02  BBG_EQUITY_EXCH_US  POST_MARKET             a   0.0.12345        160000                  200000  America/New_York         0                    @US_DEFAULT
4   2024-01-01   2024-01-02  BBG_EQUITY_EXCH_US      HOLIDAY             H       1.3.1             0                  240000  America/New_York         1  MARTIN_LUTHER_KING@US_DEFAULT
..         ...          ...                 ...          ...           ...         ...           ...                     ...               ...       ...                            ...
```

Set symbol name with `symbol` parameter:

```
>>> db = otp.databases()['US_COMP_SAMPLE']
>>> db.ref_data(ref_data_type='corp_actions',
...             start=otp.dt(2024, 1, 1),
...             end=otp.dt(2024, 4, 1),
...             symbol_date=otp.dt(2024, 1, 1),
...             symbol='AAPL',
...             timezone='America/New_York')
        Time  MULTIPLICATIVE_ADJUSTMENT  ADDITIVE_ADJUSTMENT ADJUSTMENT_TYPE
0 2024-02-09                   1.000000                 0.24   CASH_DIVIDEND
1 2024-02-09                   0.998726                 0.00  MULTI_ADJ_CASH
```

##### SEE ALSO
**REF_DATA** OneTick event processor
