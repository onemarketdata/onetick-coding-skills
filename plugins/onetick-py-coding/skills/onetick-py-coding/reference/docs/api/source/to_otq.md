# otp.Source.to_otq

#### ``Source.to_otq(file_name=None, file_suffix=None, query_name=None, symbols=None, start=None, end=None, timezone=None, add_passthrough=True, running=None, start_time_expression=None, end_time_expression=None, symbol_date=None, concurrency=None, batch_size=None, query_properties=None)``

Save ``otp.Source`` object to .otq file and return path to the saved file.

* **Parameters:**
  * **file_name** (*str*) – Absolute or relative path to the saved file.
    If `None`, create temporary file and name it randomly.
  * **file_suffix** (*str*) – Suffix to add to the saved file name (including extension).
    Can be specified if `file_name` is `None`
    to distinguish between different temporary files.
    Default: “.to_otq.otq”
  * **query_name** (*str*) – Name of the main query in the created file.
    If `None`, take name from this Source object.
    If that name is empty, set name to “query”.
  * **symbols** (str, list, `DataFrame`, ``Source``) – symbols to save query with
  * **start** (``otp.datetime``) – start time to save query with
  * **end** (``otp.datetime``) – end time to save query with
  * **timezone** (*str*) – timezone to save query with
  * **add_passthrough** (*bool*) – will add `onetick.query.Passthrough` event processor at the end of resulting graph
  * **running** (*bool*) – Indicates whether a query is CEP or not.
  * **start_time_expression** (str, ``Operation``, optional) – Start time onetick expression of the query. If specified, it will take precedence over `start`.
  * **end_time_expression** (str, ``Operation``, optional) – End time onetick expression of the query. If specified, it will take precedence over `end`.
  * **symbol_date** (``otp.datetime`` or ``datetime.datetime`` or int) – Symbol date for the query or integer in the YYYYMMDD format.
    Will be applied only to the main query.
  * **concurrency** (*int*) – Concurrency set for the query.
    Will be applied only to the main query.
  * **batch_size** (*int*) – Batch size set for the query.
    Will be applied only to the main query.
  * **query_properties** (`pyomd.QueryProperties` or dict, optional) – Query properties, such as ONE_TO_MANY_POLICY, ALLOW_GRAPH_REUSE, etc
* **Returns:**
  **result** – Relative (if `file_name` is relative) or absolute path to the created query
  in the format `file_name::query_name`
* **Return type:**
  `str`

##### Examples

Create the .otq file from a ``otp.Source`` object:

```
>>> t = otp.Tick(A=1)
>>> t.to_otq()  
'/tmp/test_user/run_20251202_181018_11054/impetuous-bullfrog.to_otq.otq::query'
```
