# otp.Source.cache

#### ``Source.cache(cache_name, delete_if_exists=True, inheritability=True, otq_params=None, time_granularity=0, time_granularity_units=None, timezone='', time_intervals_to_cache=None, allow_delete_to_everyone=False, allow_update_to_everyone=False, allow_search_to_everyone=True, cache_expiration_interval=0, start=None, end=None, read_mode='automatic', update_cache=True, tick_type='ANY', symbol=None, db=None)``

Create cache from query and ``onetick.py.ReadCache`` for created cache.

Cache will be created only for current session.

By default, if cache with specified name exists, it will be deleted and recreated.
You can change this behaviour via `delete_if_exists` parameter.

* **Parameters:**
  * **cache_name** (*str*) – Name of the cache to be deleted.
  * **delete_if_exists** (*bool*) – If set to `True` (default), a check will be made to detect the existence of a cache
    with the specified name. Cache will be deleted and recreated only if it exists.
    If set to `False`, if cache exists it won’t be deleted and recreated.
  * **inheritability** (*bool*) – Indicates whether results can be obtained by combining time intervals that were cached with intervals
    freshly computed to obtain results for larger intervals.
  * **otq_params** (*dict*) – OTQ params of the query to be cached.
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
  * **start** (``otp.datetime``) – Start time for cache query. By default, the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for cache query. By default, the end time of the query will be used.
  * **read_mode** (*str*) – 

    Mode of querying cache. One of these:
    * `cache_only` - only cached results are returned and queries are not performed.
    * `query_only` - the query is run irrespective of whether the result is already available in the cache.
    * `automatic` (default) - perform the query if the data is not found in the cache.
  * **update_cache** (*bool*) – If set to `True`, updates the cached data if `read_mode=query_only` or if `read_mode=automatic` and
    the result data not found in the cache. Otherwise, the cache remains unchanged.
  * **tick_type** (*str*) – Tick type.
  * **symbol** (str, list of str, list of otq.Symbol, ``onetick.py.Source``, `pandas.DataFrame`, optional) – `symbols` parameter of `otp.run()`.
  * **db** (*str*) – Database.
  * **self** (*Source*)
* **Return type:**
  `Source`

##### Examples

Simple usage

```
>>> src = otp.DataSource("COMMON", tick_type="TRD", symbols="AAPL")
>>> data = src.cache(
...    cache_name="some_cache",
...    tick_type="TRD", symbol="SYM", db="LOCAL",
... )
>>> df = otp.run(data)  
```

##### SEE ALSO
``onetick.py.create_cache()``

``onetick.py.ReadCache``

