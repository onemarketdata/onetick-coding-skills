# otp.DataFile

### ``class DataFile(file=None, msg_format='arrow', symbol_name_field='SYMBOL_NAME', symbology=None, timestamp_column=None, time_assignment=utils.adaptive, file_contents=None, format_file=None, config_dir=None, start=utils.adaptive, end=utils.adaptive, db=utils.adaptive, tick_type=utils.adaptive, symbols=utils.adaptive_to_default, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Reads data streams in supported formats from file or file content,
processes these streams to generate ticks,
and queries the result against a list of symbols.

Continuous event processing (CEP) of the data file is currently supported only for the JSON format.
For other formats, or if the file content is specified,
EP will function without continuous processing (no exception will be thrown).

* **Parameters:**
  * **file** (*str* *|* *None*) – 

    Specifies the path of the file to process.
    The parameter **DATA_FILE_PATH** in the OneTick configuration file specifies the set of directories
    where this file is searched for if the value of this parameter is a relative path.

    When run on a tick server with the **TICK_SERVER_DATA_CACHE_DIR** OneTick configuration variable
    pointing to a directory,
    this method will attempt to fetch the file from the host
    if the file is not found locally.
    Fetched files will be cached in this directory.

    To use AWS S3 file system paths,
    the **AWS_ACCESS_KEY_ID** and **AWS_SECRET_ACCESS_KEY** environment variables must be set.
    Note: Currently, AWS S3 is supported only for the `arrow` format.
  * **msg_format** (*Literal* *[* *'arrow'* *,*  *'json'* *]*) – Specifies the format of input data: *arrow* or *json*.
  * **symbol_name_field** (*str*) – Defines the field expected to contain the symbol name.
    Its data type should be either string or varstring.
    When one or more symbols are specified for the query or this method,
    only ticks corresponding to those symbols will be propagated.
  * **symbology** (*str* *|* *None*) – Defines the symbology of symbol names (values of `symbol_name_field`) in the data file.
    If specified, to find correct time series in the file for the query symbol
    this method will first search for synonym[s] of the symbols
    in the specified symbology using reference database.
    Then it will find the history of this new symbol for the query interval
    and finally will query the data file using the symbol names from history.
  * **timestamp_column** (*str* *|* *None*) – If provided, the value of this field will be used to set the timestamps for the ticks.
    In the case of `msg_format=arrow`, the Arrow type of this field must be DATE64, TIMESTAMP
    or INT64 with metadata “TIMESTAMP_TYPE=NANO/MILLI”.
  * **time_assignment** – Specifies whether the timestamps for the ticks are set to either the start time or end time of the query.
  * **file_contents** (*str* *|* *None*) – If not empty, this method treats its value as a stream from which it should read and construct ticks.
  * **format_file** (*str* *|* *None*) – Specifies the absolute path of the message format file. This parameter applies only to *json* format.
  * **config_dir** (*str* *|* *None*) – Specifies the directory of the normalization file. This parameter applies only to *json* format.
  * **start** – Custom start time of the query.
    By default, the start time used by ``otp.run`` will be inherited.
  * **end** – Custom end time of the query.
    By default, the start time used by ``otp.run`` will be inherited.
  * **db** (*str*) – Custom database name for the node of the graph.
    By default, the database used by ``otp.run`` will be inherited.
  * **tick_type** (*str*) – Custom tick type for the node of the graph.
    By default, “ANY” tick type will be set.
  * **symbols** (*str* *or* *list* *of* *str*) – Custom symbol name for the node of the graph.
    By default, the symbol name used by ``otp.run`` will be inherited.
  * **schema** (*dict*) – 

    Set the schema of the python ``Source`` object of this class.

    Schema can’t be automatically derived from the file, so it should be set manually
    for Python-level type checking to work.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** – Deprecated. Use `schema` instead.
    Set the schema of the python ``Source`` object of this class.

#### NOTE
The method is supported only on 64-bit Windows/Linux platforms.

##### Examples

Get data from the arrow file:

```python
import os
path_to_arrow_file = os.path.join(csv_path, 'data.arrow')

data = otp.DataFile(path_to_arrow_file)
df = otp.run(data)
print(df)
```

```
        Time  A SYMBOL_NAME              T_TIME
0 2003-12-04  1        AAPL 2003-12-01 00:00:00
1 2003-12-04  3        AAPL 2003-12-01 02:00:00
```

The `symbol_name_field` parameter can be used to specify the name of the field containing symbol names.
The default value for this parameter is **SYMBOL_NAME**, and this field must be specified in the Arrow file.
The symbols specified for the query will determine which data will be queried from the Arrow file:

```python
data = otp.DataFile(path_to_arrow_file)
df = otp.run(data, symbols='MSFT')
print(df)
```

```
        Time  A SYMBOL_NAME              T_TIME
0 2003-12-04  2        MSFT 2003-12-01 01:00:00
```

To get all symbols from file, you can specify a database without a symbol when running query:

```python
data = otp.DataFile(path_to_arrow_file)
df = otp.run(data, symbols=f'{otp.config.default_db}::')
print(df)
```

```
        Time  A SYMBOL_NAME              T_TIME
0 2003-12-04  1        AAPL 2003-12-01 00:00:00
1 2003-12-04  2        MSFT 2003-12-01 01:00:00
2 2003-12-04  3        AAPL 2003-12-01 02:00:00
```

Default time assigned to ticks is query end time.
Use parameter `time_assignment` to change the timestamps for ticks to the query start time:

```python
data = otp.DataFile(path_to_arrow_file, time_assignment='start')
df = otp.run(data)
print(df)
```

```
        Time  A SYMBOL_NAME              T_TIME
0 2003-12-01  1        AAPL 2003-12-01 00:00:00
1 2003-12-01  3        AAPL 2003-12-01 02:00:00
```

Or use parameter `timestamp_column` to get the timestamps from some field:

```python
data = otp.DataFile(path_to_arrow_file, timestamp_column='T_TIME')
df = otp.run(data)
print(df)
```

```
                 Time  A SYMBOL_NAME              T_TIME
0 2003-12-01 00:00:00  1        AAPL 2003-12-01 00:00:00
1 2003-12-01 02:00:00  3        AAPL 2003-12-01 02:00:00
```

The schema of the data can’t be checked when constructing query at the Python level,
so in order to use the fields of the source in other onetick-py methods,
schema must be specified manually:

```python
data = otp.DataFile(path_to_arrow_file,
                    schema={'A': int, 'SYMBOL_NAME': str, 'T_TIME': otp.nsectime})
data['B'] = data['A'] * 2
df = otp.run(data)
print(df)
```

```
        Time  A SYMBOL_NAME              T_TIME  B
0 2003-12-04  1        AAPL 2003-12-01 00:00:00  2
1 2003-12-04  3        AAPL 2003-12-01 02:00:00  6
```

##### SEE ALSO
**DATA_FILE_QUERY** OneTick event processor
