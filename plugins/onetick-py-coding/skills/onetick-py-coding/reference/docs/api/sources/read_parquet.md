# otp.ReadParquet

### ``class ReadParquet(parquet_file_path=None, where=None, time_assignment='end', discard_fields=None, fields=None, symbol_name_field=None, symbol=utils.adaptive, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Read ticks from Parquet file

* **Parameters:**
  * **parquet_file_path** (*str*) – Specifies the path or URL of the Parquet file to read.
  * **where** (*str* *,* *None*) – Specifies a criterion for selecting the ticks to propagate.
  * **time_assignment** (*str*) – Timestamps of the ticks created by ReadParquet are set to the start/end of the query or to the given
    tick field depending on the time_assignment parameter.
    Possible values are start and end (for \_START_TIME and \_END_TIME) or a field name.
    Default: end
  * **discard_fields** (*list* *,* *str* *,* *None*) – A list of fields (list or comma-separated string) to be discarded from the output ticks.
  * **fields** (*list* *,* *str* *,* *None*) – A list of fields (list or comma-separated string) to be picked from the output ticks.
    The opposite to discard_fields.
  * **symbol_name_field** (*str* *,* *None*) – Field that is expected to contain the symbol name.
    When this parameter is set and one or more symbols containing time series
    (i.e. [dbname]::[time series name] and not just [dbname]::) are bound to the query or to this EP,
    only rows belonging to those symbols will be propagated.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **start** (``otp.datetime``) – Start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for tick generation. By default the end time of the query will be used.
  * **schema** (*dict*) – Dictionary of columns names with their types.
    You should set schema manually, if you want to use fields in onetick-py query description
    before its execution.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** – Deprecated. Use `schema` instead.
    Dictionary of columns names with their types.

##### Examples

Simple Parquet file read:

```
>>> data = otp.ReadParquet("/path/to/parquet/file")
>>> otp.run(data)  
```

Read Parquet file and filter fields:

```
>>> data = otp.ReadParquet("/path/to/parquet/file", fields=["some_field", "another_field"])
>>> otp.run(data)  
```

Read Parquet file and filter rows:

```
>>> data = otp.ReadParquet("/path/to/parquet/file", where="PRICE > 20")
>>> otp.run(data)  
```

##### SEE ALSO
**READ_FROM_PARQUET** OneTick event processor

``onetick.py.Source.write_parquet()``

