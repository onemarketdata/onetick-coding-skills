# otp.functions.save_sources_to_single_file

### ``save_sources_to_single_file(sources, file_path=None, file_suffix='', start=None, end=None, start_time_expression=None, end_time_expression=None, timezone=None, running_query_flag=None)``

Save onetick.py.Source objects to the single file.

* **Parameters:**
  * **sources** (*dict* *or* *list*) – 

    dict of names -> sources or list of sources to merge into single file.
    If it’s the list then names will be auto-generated.
    Source can be `otp.Source` object or dictionary with these allowed parameters:
    * source: **otp.Source** - source object
    * start: **otp.datetime**, optional - query start time
    * end: **otp.datetime**, optional - query end time
    * symbols: **otp.Source** or **otp.Symbols**, optional - query symbols
    * query_properties: **dict[str, str]**, optional - query properties
  * **file_path** (*str* *,* *optional*) – Path to the file where all sources will be saved.
    If not set, sources will be saved to temporary file and its name will be returned.
  * **file_suffix** (*str*) – Only used if `file_path` is not set.
    This suffix will be added to the name of a generated query file.
  * **start** (*datetime* *,* *optional*) – start time for the resulting query file
  * **end** (*datetime* *,* *optional*) – end time for the resulting query file
  * **start_time_expression** (*str* *,* *optional*) – start time expression for the resulting query file
  * **end_time_expression** (*str* *,* *optional*) – end time expression for the resulting query file
  * **timezone** (*str* *,* *optional*) – timezone for the resulting query file
  * **running_query_flag** (*bool* *,* *optional*) – running query flag for the resulting query file
* **Returns:**
  * If sources is list then returns list of full query paths (path_to_file::query_name)
  * with auto-generated names corresponding to each source from sources.
  * If sources is dict then the path to the query file is returned.

##### Examples

Save multiple sources src1 and src2 to one file /home/test/queries.otq as query_1 and query_2:

```
>>> otp.functions.save_sources_to_single_file(
...     {
...         'query_1': src1,
...         'query_2': src2,
...     },
...     file_path='/home/test/queries.otq',
... )  
```

Set start and end time for query_1 and symbol name for query_2:

```
>>> otp.functions.save_sources_to_single_file(
...     {
...         'query_1': {
...             'source': src1,
...             'start': otp.datetime(2024, 1, 1),
...             'end': otp.datetime(2024, 1, 2),
...         },
...         'query_2': {
...             'source': src2,
...             'symbols': otp.Tick(SYMBOL_NAME='AAPL'),
...         }
...     },
...     file_path='/home/test/queries.otq',
... )  
```

Set query property MAX_CONCURRENCY for query_1:

```
>>> otp.functions.save_sources_to_single_file(
...     {
...         'query_1': {
...             'source': src1,
...             'query_properties': {
...                 'MAX_CONCURRENCY': '4',
...             },
...         },
...         'query_2': src2,
...     },
...     file_path='/home/test/queries.otq',
... )  
```
