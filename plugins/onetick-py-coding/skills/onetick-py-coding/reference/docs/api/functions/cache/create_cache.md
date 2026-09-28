# otp.create_cache

### ``create_cache(cache_name, query=None, inheritability=True, otq_params=None, time_granularity=0, time_granularity_units=None, timezone='', time_intervals_to_cache=None, allow_delete_to_everyone=False, allow_update_to_everyone=False, allow_search_to_everyone=True, cache_expiration_interval=0, tick_type='ANY', symbol=None, db=None)``

Create cache via CREATE_CACHE EP

If ``onetick.py.Source`` or callable passed as `query` parameter,
cache will be created only for current session.

Also, this case or passing absolute path to .otq as `query` parameter are supported
only for caching on local OneTick server.

Parameters `symbol`, `db` and `tick_type` could be omitted if you want to use
default symbol, default database and `ANY` tick type.

* **Parameters:**
  * **cache_name** (*str*) – Name of the cache to be created.
  * **query** (``onetick.py.Source``, callable, str) – Query to be cached or path to .otq file (in `filename.otq::QueryName` format).
    Only queries residing on the server that runs the caching event processors are currently supported.
    For local OneTick server you can pass absolute path to local .otq file.
  * **inheritability** (*bool*) – Indicates whether results can be obtained by combining time intervals that were cached with intervals
    freshly computed to obtain results for larger intervals.
  * **otq_params** (*dict*) – OTQ params of the query to be cached.
    Setting otq_params in ``onetick.py.ReadCache`` may not override this otq_params
    if cache not invalidated.
  * **time_granularity** (*int*) – Value N for seconds/days/months granularity means that start and end time of the query have to be on N
    second/day/month boundaries relative to start of the day/month/year.
    This doesn’t affect the frequency of data within the cache, just the start and end dates.
  * **time_granularity_units** (*str* *,* *None*) – Units used in `time_granularity` parameter. Possible values: ‘none’, ‘days’, ‘months’, ‘seconds’ or None.
  * **timezone** (*str*) – Timezone of the query to be cached.
  * **time_intervals_to_cache** (*list* **[*tuple* *]*) – 

    List of tuples with start and end times in `[(<start_time_1>, <end_time_1>), ...]` format,
    where `<start_time>` and `<end_time>` should be one of these:
    * string in `YYYYMMDDhhmmss[.msec]` format.
    * ``datetime.datetime``
    * ``onetick.py.types.datetime``

    If specified only these time intervals can be cached. Ignored if `inheritability=True`.
    If you try to make a query outside defined interval, error will be raised.
  * **allow_delete_to_everyone** (*bool*) – When set to `True` everyone is allowed to delete the cache.
  * **allow_update_to_everyone** (*bool*) – When set to `True` everyone is allowed to update the cache.
  * **allow_search_to_everyone** (*bool*) – When set to `True` everyone is allowed to read the cached data.
  * **cache_expiration_interval** (*int*) – If set to a non-zero value determines the periodicity of cache clearing, in seconds.
    The cache will be cleared every X seconds, triggering new query executions when data is requested.
  * **tick_type** (*str*) – Tick type.
  * **symbol** (str, list of str, list of otq.Symbol, ``onetick.py.Source``, `pandas.DataFrame`, optional) – `symbols` parameter of `otp.run()`.
  * **db** (*str*) – Database.

#### NOTE
This function does NOT populate the cache, it’s only reserves the cache name in the OneTick memory.
Cache is only populated when an attempt is made to read the data from it via ``onetick.py.ReadCache``.

##### Examples

Simple cache creation from .otq file on OneTick server under `OTQ_FILE_PATH`

```
>>> otp.create_cache(  
...    cache_name="some_cache", query="CACHE_EXAMPLE.otq::slowquery",
...    tick_type="TRD", db="LOCAL",
... )
```

Cache creation from function

```
>>> def query_func():
...    return otp.DataSource("COMMON", tick_type="TRD", symbols="AAPL")
>>> otp.create_cache(
...    cache_name="some_cache", query=query_func, tick_type="TRD", db="LOCAL",
... )
```

Create cache for time intervals with different datetime types:

```
>>> otp.create_cache(  
...    cache_name="some_cache",
...    query="query_example.otq::query"),
...    inheritability=False,
...    time_intervals_to_cache=[
...        ("20220601123000.000000", "20220601183000.000000"),
...        (datetime(2022, 6, 2, 12, 30), datetime(2003, 1, 2, 18, 30)),
...        (otp.datetime(2022, 6, 3, 12, 30), otp.datetime(2022, 6, 3, 18, 30)),
...    ],
...    timezone="GMT",
...    tick_type="TRD",
...    db="LOCAL",
... )
```

Create cache with OTQ params:

```
>>> otp.create_cache(  
...    cache_name="some_cache",
...    query="query_example.otq::query"),
...    otq_params={"some_param": "some_value"},
...    timezone="GMT",
...    tick_type="TRD",
...    db="LOCAL",
... )
```

##### SEE ALSO
**CREATE_CACHE** OneTick event processor

``onetick.py.ReadCache``

``onetick.py.delete_cache()``

