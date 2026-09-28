# otp.CSV

### ``class CSV(filepath_or_buffer=None, timestamp_name='Time', first_line_is_title=True, names=None, dtype=None, converters=None, order_ticks=False, drop_index=True, change_date_to=None, auto_increase_timestamps=True, db=utils.adaptive_to_default, field_delimiter=',', handle_escaped_chars=False, quote_char='"', timestamp_format=None, file_contents=None, use_field_delimiters_for_title=None, query_parameters=None, **kwargs)``

Bases:

Construct source based on CSV file.

There are several steps determining column types.

1. Initially, all column treated as `str`.
2. If column name in CSV title have format `type COLUMN_NAME`,
   it will change type from `str` to specified type.
3. All column type are determined automatically from its data.
4. You could override determined types in `dtype` argument explicitly.
5. `converters` argument is applied after `dtype` and could also change column type.

NOTE: Double quotes are not supported in CSV files for escaping quotes in strings,
you should use escape character `\` before the quote instead,
for example: `"I'm a string with a \"quotes\" inside"`. And then set `handle_escaped_chars=True`.

* **Parameters:**
  * **filepath_or_buffer** (*str* *,* *os.PathLike* *,* *FileBuffer* *,* *optional*) – Path to CSV file or `file buffer`. If None value is taken through symbol.
    When taken from symbol, symbol must have `LOCAL::` prefix.
    In that case you should set the columns otherwise schema will be empty.
  * **timestamp_name** (*str* *,* *default "Time"*) – Name of TIMESTAMP column used for ticks. Used only if it is exists in CSV columns, otherwise ignored.
    Output data will be sorted by this column.
  * **first_line_is_title** (*bool*) – 

    Use first line of CSV file as a source for column names and types.
    If CSV file is started with # symbol, this parameter **must** be `True`.
    - If `True`, column names are inferred from the first line of the file,
      it is not allowed to have empty name for any column.
    - If `False`, first line is processed as data, column names will be COLUMN_1, …, COLUMN_N.
      You could specify column names in `names` argument.
  * **names** (*list* *,* *optional*) – List of column names to use, or None.
    Length must be equal to columns number in file.
    Duplicates in this list are not allowed.
  * **dtype** (*dict* *,* *optional*) – Data type for columns, as dict of pairs {column_name: type}.
    Will convert column type from `str` to specified type, before applying converters.
  * **converters** (*dict* *,* *optional*) – 

    Dict of functions for converting values in certain columns. Keys are column names.
    Function must be valid callable with `onetick.py` syntax, example:
    ```
    converters={
        "time_number": lambda c: c.apply(otp.nsectime),
        "stock": lambda c: c.str.lower(),
    }
    ```

    Converters applied *after* `dtype` conversion.
  * **order_ticks** (*bool* *,* *optional*) – If `True` and `timestamp_name` column are used, then source will order tick by time.
    Note, that if `False` and ticks are not ordered in sequence, then OneTick will raise Exception in runtime.
  * **drop_index** (*bool* *,* *optional*) – if `True` and ‘Index’ column is in the csv file then this column will be removed.
  * **change_date_to** (*datetime* *,* *date* *,* *optional*) – change date from a timestamp column to a specific date. Default is None, means not changing timestamp column.
  * **auto_increase_timestamps** (*bool* *,* *optional*) – Only used if provided CSV file does not have a TIMESTAMP column. If `True`, timestamps of loaded ticks
    would start at `start_time` and on each next tick, would increase by 1 millisecond.
    If `False`, timestamps of all loaded ticks would be equal to `start_time`
  * **db** (*str* *,* *optional*) – Name of a database to define a destination where the csv file will be transported for processing.
    By default ``otp.config.default_db`` is used.
    `LOCAL` value means that OneTick will process it on the site where a query runs.
  * **field_delimiter** (*str* *,* *optional*) – A character that is used to tokenize each line of the CSV file.
    For a tab character `\t` (back-slash followed by t) should be specified.
  * **handle_escaped_chars** (*bool* *,* *optional*) – If set, the backslash char `\` gets a special meaning and everywhere in the input text
    the combinations `\'`, `\"` and `\\` are changed correspondingly by `'`, `"` and `\`,
    which are processed then as regular chars.
    Besides, combinations like `\x??`, where ?-s are hexadecimal digits (0-9, a-f or A-F),
    are changed by the chars with the specified ASCII code.
    For example, `\x0A` will be replaced by a newline character, `\x09` will be replaced by tab, and so on.
    Default: False
  * **quote_char** (*str*) – Character used to denote the start and end of a quoted item. Quoted items can include the delimiter,
    and it will be ignored. The same character cannot be marked both as the quote character and as the
    field delimiter. Besides, space characters cannot be used as quote.
    To disable quoting, set this parameter to an empty string.
    Default: `"` (double quotes)
  * **timestamp_format** (*str* *or* *dict*) – Expected format for `timestamp_name` and all other datetime columns.
    If dictionary is passed, then different format can be specified for each column.
    This format is expected when converting strings from csv file to `dtype`.
    Default format is `%Y/%m/%d %H:%M:%S.%J` for ``nsectime`` columns and
    `%Y/%m/%d %H:%M:%S.%q` for ``msectime`` columns.
  * **file_contents** (*str*) – Specify the contents of the csv file as string.
    Can be used instead of `filepath_or_buffer` parameter.
  * **use_field_delimiters_for_title** (*bool*) – Use values from `field_delimiter` parameter in the title too.
    Default is False, in this case only comma `,` and space \`\` \`\` characters are used as delimiters in the title.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Simple CSV file reading

```
>>> data = otp.CSV(os.path.join(csv_path, "data.csv"))
>>> otp.run(data)
                     Time          time_number      px side
0 2003-12-01 00:00:00.000  1656690986953602371   30.89  Buy
1 2003-12-01 00:00:00.001  1656667706281508365  682.88  Buy
```

Read CSV file and get timestamp for ticks from specific field.
You need to specify query start/end interval including all ticks.

```
>>> data = otp.CSV(os.path.join(csv_path, "data.csv"),
...                timestamp_name="time_number",
...                converters={"time_number": lambda c: c.apply(otp.nsectime)},
...                start=otp.dt(2010, 8, 1),
...                end=otp.dt(2022, 9, 2))
>>> otp.run(data)
                           Time      px side
0 2022-07-01 05:28:26.281508365  682.88  Buy
1 2022-07-01 11:56:26.953602371   30.89  Buy
```

Path to csv can be passed via symbol when running the query:

```
>>> data = otp.CSV()
>>> otp.run(data, symbols=os.path.join(csv_path, 'data.csv'))
                     Time          time_number      px side
0 2003-12-01 00:00:00.000  1656690986953602371   30.89  Buy
1 2003-12-01 00:00:00.001  1656667706281508365  682.88  Buy
```

Field delimiters can be set via `field_delimiters` parameter:

```
>>> data = otp.CSV(os.path.join(csv_path, 'data_diff_delimiters.csv'),
...                field_delimiter=' ',
...                first_line_is_title=False)
>>> otp.run(data)
                     Time COLUMN_0 COLUMN_1
0 2003-12-01 00:00:00.000      1,2        3
1 2003-12-01 00:00:00.001        4      5,6
```

Quote char can be set via `quote_char` parameter:

```
>>> data = otp.CSV(os.path.join(csv_path, 'data_diff_quote_chars.csv'),
...                quote_char="'",
...                first_line_is_title=False)
>>> otp.run(data)
                     Time COLUMN_0 COLUMN_1
0 2003-12-01 00:00:00.000     1,"2       3"
1 2003-12-01 00:00:00.001       "1     2",3
```

Use parameter `file_contents` to read the data from string:

```
>>> data = otp.CSV(file_contents=os.linesep.join([
...     'A,B,C',
...     '1,f,3.3',
...     '2,g,4.4',
... ]))
>>> otp.run(data)
                     Time  A  B    C
0 2003-12-01 00:00:00.000  1  f  3.3
1 2003-12-01 00:00:00.001  2  g  4.4
```

Use parameter `use_field_delimiters_for_title` to use `field_delimiter` in title too:

```
>>> file_contents = os.linesep.join([
...     'A  B',
...     '1  3',
...     '2  4',
... ])
>>> print(file_contents)
A  B
1  3
2  4
>>> data = otp.CSV(file_contents=file_contents,
...                field_delimiter='        ',
...                use_field_delimiters_for_title=True)  
>>> otp.run(data)  
                     Time  A  B
0 2003-12-01 00:00:00.000  1  3
1 2003-12-01 00:00:00.001  2  4
```

##### SEE ALSO
**CSV_FILE_LISTING** OneTick event processor

## otp.utils.file

### ``file(path)``

Helps to build a file buffer that could be used to
delivery on the remote site to be processed there.
For example it could be passed as input to the ``CSV``

* **Parameters:**
  **path** (*str* *|* *PathLike*)
* **Return type:**
  *FileBuffer*

## otp.utils.FileBuffer

### ``class FileBuffer(path)``

Bases: ``object``

Class holds the file content with goal to delivery
it to the execution side in case of remote executions.

The basic implementation reads file content to a property
that allows to transfer file content as pickled object
to the server side since the pickling stores all class property
values.

* **Parameters:**
  **path** (*str* *|* *PathLike*)
