# otp.Source.write

#### ``Source.write(db, symbol=None, tick_type=None, date=adaptive, start_date=None, end_date=None, append=False, keep_symbol_and_tick_type=adaptive, propagate=True, out_of_range_tick_action='exception', timestamp=None, keep_timestamp=True, correction_type=None, replace_existing_time_series=False, allow_concurrent_write=False, context=adaptive, use_context_of_query=False, inplace=False, **kwargs)``

Saves data result to OneTick database.

* **Parameters:**
  * **db** (str or ``otp.DB``) – database name or object.
  * **symbol** (*str* *or* *Column*) – resulting symbol name string or column to get symbol name from.
    If this parameter is not set, then ticks \_SYMBOL_NAME pseudo-field is used.
    If it is empty, an attempt is made to retrieve
    the symbol name from the field named SYMBOL_NAME.
  * **tick_type** (*str* *or* *Column*) – resulting tick type string or column to get tick type from.
    If this parameter is not set, the \_TICK_TYPE pseudo-field is used.
    If it is empty, an attempt is made to retrieve
    the tick type from the field named TICK_TYPE.
  * **date** (``otp.datetime`` or None) – date where to save data.
    Should be set to None if writing to accelerator or memory database.
    By default, it is set to otp.config.default_date.
  * **start_date** (``otp.datetime`` or None) – Start date for data to save. It is inclusive.
    Cannot be used with `date` parameter.
    Also cannot be used with `inplace` set to `True`.
    Should be set to None if writing to accelerator or memory database.
    By default, None.
  * **end_date** (``otp.datetime`` or None) – End date for data to save. It is inclusive.
    Cannot be used with `date` parameter.
    Also cannot be used with `inplace` set to `True`.
    Should be set to None if writing to accelerator or memory database.
    By default, None.
  * **append** (*bool*) – If False - data will be rewritten for this `date`
    or range of dates (from `start_date` to `end_date`),
    otherwise data will be appended: new symbols are added,
    existing symbols can be modified (append new ticks, modify existing ticks).
    This option is not valid for accelerator databases.
  * **keep_symbol_and_tick_type** (*bool*) – keep fields containing symbol name and tick type when writing ticks
    to the database or propagating them.
    By default, this parameter is adaptive.
    If `symbol` or `tick_type` are column objects, then it’s set to True.
    Otherwise, it’s set to False.
  * **propagate** (*bool*) – Propagate ticks after that event processor or not.
  * **out_of_range_tick_action** (*str*) – 

    Action to be executed if tick’s timestamp’s date is not `date` or between `start_date` or `end_date`:
    > * exception: runtime exception will be raised
    > * ignore: tick will not be written to the database
    > * load: writes tick to the database anyway.
    >   : Can be used only with `date`, not with `start_date``+``end_date`.

    Default: exception
  * **timestamp** (*Column*) – Field that contains the timestamp with which the ticks will be written to the database.
    By default, the TIMESTAMP pseudo-column is used.
  * **keep_timestamp** (*bool*) – If `timestamp` parameter is set and this parameter is set to True,
    then timestamp column is removed.
  * **correction_type** (*Column*) – The name of the column that contains the correction type.
    This column will be removed.
    If this parameter is not set, no corrections will be submitted.
  * **replace_existing_time_series** (*bool*) – If `append` is set to True, setting this option to True instructs the loader
    to replace existing time series, instead of appending to them.
    Other time series will remain unchanged.
  * **allow_concurrent_write** (*bool*) – Allows different queries running on the same server to load concurrently into the same database.
  * **context** (*str*) – The server context used to look up the database.
    By default, otp.config.context is used if `use_context_of_query` is not set.
  * **use_context_of_query** (*bool*) – If this parameter is set to True and the `context` parameter is not set,
    the context of the query is used instead of the default value of the `context` parameter.
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
    Cannot be `True` if `start_date` and `end_date` are set.
  * **kwargs** – 

    #### Deprecated
    Deprecated since version 1.21.0.

    Use named parameters instead.
* **Return type:**
  ``Source`` or None

#### NOTE
This method does not save anything. It adds instruction in query to save.
Data will be saved when query will be executed.

Using `start``+``end` parameters instead of single `date` have some limitations:

> * `inplace` is not supported
> * if `DAY_BOUNDARY_TZ` and `DAY_BOUNDARY_OFFSET` specified against
>   : individual locations of database, then day boundary could be calculated incorrectly.
> * `out_of_range_tick_action` could be only `exception` or `ignore`

##### Examples

Write to the database:

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data = data.write('WRITE_DB', symbol='S_WRITE', tick_type='T_WRITE')
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

Then read from the same database:

```
>>> data = otp.DataSource('WRITE_DB', symbol='S_WRITE', tick_type='T_WRITE')
>>> otp.run(data)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

##### SEE ALSO
**WRITE_TO_ONETICK_DB** OneTick event processor
