# otp.Source.write_text

#### ``Source.write_text(*, propagate_ticks=True, output_headers=True, output_types_in_headers=False, order=None, prepend_symbol_name=True, prepended_symbol_name_size=0, prepend_timestamp=True, separator=',', formats_of_fields=None, double_format='%f', output_dir=None, output_file=None, error_file=None, warning_file=None, data_quality_file=None, treat_input_as_binary=False, flush=True, append=False, allow_concurrent_write=False, inplace=False)``

Writes the input tick series to a text file or standard output.

* **Parameters:**
  * **propagate_ticks** (*bool*) – If True (default) then ticks will be propagated after this method, otherwise this method won’t return ticks.
  * **output_headers** (*bool*) – Switches the output of the headers.
    If True (default), a tick descriptor line appears in the output before the very first tick for that query.
    If the structure of the output tick changes, another tick descriptor line appears before the first changed tick.
    The header line starts with **#**.
    The field names are ordered as mandated by the `order` parameter or,
    if it is empty, in the order of appearance in the tick descriptor.
    Fields that are not specified in the `order` parameter
    will appear after specified ones in the order of their appearance in the tick descriptor.
  * **output_types_in_headers** (*bool*) – Switches the output of field types in the header lines.
    `output_types_in_headers` can be set only when `output_headers` is set too.
  * **order** (*list*) – 

    The field appearance order in the output.
    If all or some fields are not specified,
    those fields will be written in the order of their appearance in the tick descriptor.

    Field **SYMBOL_NAME** may be specified if parameter `prepend_symbol_name` is set.

    Field **TIMESTAMP** may be specified if parameter `prepend_timestamp` is set.
  * **prepend_symbol_name** (*bool*) – If True (default), prepends symbol name before other fields as a new field named **SYMBOL_NAME** in the header
    (if `output_headers` is set).
  * **prepended_symbol_name_size** (*int*) – When `prepend_symbol_name` is set, symbol will be adjusted to this size.
    If set to 0 (default), no adjustment will be done.
  * **prepend_timestamp** (*bool*) – If set (default), tick timestamps, formatted as *YYYYMMDDhhmmss.qqqqqq* in the GMT time zone,
    will be prepended to the output lines.
    Header lines, if present, will have **TIMESTAMP** as the first field name.
    The default output format for tick timestamps can be specified in the `formats_of_fields` parameter.
  * **separator** (*str*) – The delimiter string. This doesn’t have to be a single character.
    Escape sequences are allowed for **\\t** (tab), **\\\\** (\\ character) and **\\xHH** (hex codes).
    By default “,” (comma) will be used.
  * **formats_of_fields** (*dict*) – 

    The dictionary of field names and their formatting specifications.
    The formatting specification is the same as in the standard C
    `printf` function.

    For float and decimal fields **%f** and **%.[<precision>]f** formats are only supported,
    first one being the default an outputting 6 decimal digits.

    Also if the field format starts with **%|**,
    it means that this is a timestamp field and should be in the format **%|tz|time_format_spec**,
    where the *tz* is the time zone name (if not specified GMT will be used),
    and *time_format_spec* is a custom time format specification,
    which is the same as the one used by the
    `strftime` function.

    In addition, you can also use **%q** , **%Q** , **%k** and **%J** placeholders,
    which will be replaced by 3 and 2 sign milliseconds, 6 sign microseconds and 9 sign nanoseconds, respectively.

    **%#**, **%-**, **%U**, **%N** placeholders will be replaced by Unix timestamp, Unix timestamp in milliseconds,
    microseconds and nanoseconds, respectively.

    **%+** and **%~** placeholders will be replaced by milliseconds and nanoseconds passed since midnight.
  * **double_format** (*str*) – This format will be used for fields that are holding double values
    if they are not specified in `formats_of_fields`.
  * **output_dir** (*str*) – If specified, all output (output, warning, error, and data quality) files will be redirected to it.
    If this directory does not exist, it will get created.
    By default, the current directory is used.
  * **output_file** (*str*) – 

    The output file name for generated text data.
    If not set, the standard output will be used.
    It is also possible to add symbol name, database name, tick type,
    date of tick and query start time to the file name.
    For this special placeholders should be used, which will be replaced with the appropriate values:
    > * **%SYMBOL%** - will be replaced with symbol name,
    > * **%DBNAME%** - with database name,
    > * **%TICKTYPE%** - with tick type,
    > * **%DATE%** - with date of tick,
    > * **%STARTTIME%** - with start time of the query.

    #### NOTE
    In case of using placeholders the output of the data may be split into different files.
    For example when querying several days of data and using **%DATE%** placeholder,
    the file will be created for every day of the interval.

    This format is also available for `error_file`, `warning_file` and `data_quality_file` input parameters.
  * **error_file** (*str*) – The file name where all error messages are directed.
    If not set the standard error will be used.
  * **warning_file** (*str*) – The file name where all warning messages are directed.
    If not set the standard error will be used.
  * **data_quality_file** (*str*) – The file name where all data quality messages are directed.
    If not set the standard error will be used.
  * **treat_input_as_binary** (*bool*) – Opens output file in binary mode to not modify content of ticks when printing them to the file.
    Also in this mode method prints no new line to the file after every tick write.
  * **flush** (*bool*) – 

    If True (default) then the output will be flushed to disk after every tick.

    #### NOTE
    Notice that while this setting makes results of the query recorded into a file without delay,
    making them immediately available to applications that read this file,
    it may slow down the query significantly.
  * **append** (*bool*) – If set to True, will try to append data to files (output, error, warning, data_quality), instead of overwriting.
  * **allow_concurrent_write** (*bool*) – Allows different queries running on the same server to write concurrently to the same files
    (output, error, warning, data_quality).
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object.
  * **self** (*Source*)

##### Examples

By default the text is written to the standard output:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> write = data.write_text()
>>> _ = otp.run(write)  
#SYMBOL_NAME,TIMESTAMP,A
AAPL,20031201050000.000000,1
AAPL,20031201050000.001000,2
AAPL,20031201050000.002000,3
```

Output file can also be specified:

```
>>> write = data.write_text(output_file='result.csv')
>>> _ = otp.run(write)  
>>> with open('result.csv') as f:  
...     print(f.read())  
#SYMBOL_NAME,TIMESTAMP,A
AAPL,20031201050000.000000,1
AAPL,20031201050000.001000,2
AAPL,20031201050000.002000,3
```

Symbol name, timestamp of the tick and can be removed from the output:

```
>>> write = data.write_text(prepend_timestamp=False,
...                         prepend_symbol_name=False)
>>> _ = otp.run(write)  
#A
1
2
3
```

The header can also be removed from the output:

```
>>> write = data.write_text(output_headers=False)
>>> _ = otp.run(write)  
AAPL,20031201050000.000000,1
AAPL,20031201050000.001000,2
AAPL,20031201050000.002000,3
```

The order of fields and separator character can be specified:

```
>>> write = data.write_text(order=['A', 'TIMESTAMP'],
...                         separator='\t',
...                         prepend_symbol_name=False)
>>> _ = otp.run(write)  
#A  TIMESTAMP
1   20031201050000.000000
2   20031201050000.001000
3   20031201050000.002000
```

The formatting can be specified for each field:

```
>>> write = data.write_text(formats_of_fields={
...     'TIMESTAMP': '%|GMT|%Y-%m-%d %H:%M:%S.%q',
...     'A': '%3d'
... })
>>> _ = otp.run(write)  
#SYMBOL_NAME,TIMESTAMP,A
AAPL,2003-12-01 05:00:00.000,  1
AAPL,2003-12-01 05:00:00.001,  2
AAPL,2003-12-01 05:00:00.002,  3
```

##### SEE ALSO
**WRITE_TEXT** OneTick event processor

``onetick.py.Source.dump()``

