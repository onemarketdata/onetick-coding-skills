# otp.ReadCache

### ``class ReadCache(cache_name=None, db=utils.adaptive_to_default, symbol=utils.adaptive, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, read_mode='automatic', update_cache=True, otq_params=None, create_cache_query='', schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Make cached query

Cache is initialized on the first read attempt.

* **Parameters:**
  * **cache_name** (*str*) – Name of cache for a query.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **db** (*str*) – Database to use for tick generation.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **start** (``otp.datetime``) – Start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for tick generation. By default the end time of the query will be used.
  * **read_mode** (*str*) – 

    Mode of querying cache. One of these:
    * `cache_only` - only cached results are returned and queries are not performed.
    * `query_only` - the query is run irrespective of whether the result is already available in the cache.
    * `automatic` (default) - perform the query if the data is not found in the cache.
  * **update_cache** (*bool*) – If set to `True`, updates the cached data if `read_mode=query_only` or if `read_mode=automatic` and
    the result data not found in the cache. Otherwise, the cache remains unchanged.
  * **otq_params** (*dict*) – OTQ parameters to override `otq_params` that are passed during creation of cache.
    Query result will be cached separately for each unique pair of OTQ parameters.
  * **create_cache_query** (*str*) – If a cache with the given name is not present, the query provided in this param will be invoked,
    which should contain CREATE_CACHE EP to create the corresponding cache.
  * **schema** (*Optional* *,* *dict*) – Dictionary of columns names with their types.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.
  * **kwargs** – Deprecated. Use `schema` instead.
    Dictionary of columns names with their types.

##### Examples

Simple cache read:

```
>>> def query_func():
...    return otp.DataSource("DEMO_L1", tick_type="TRD", symbols="AAPL")
>>> otp.create_cache(
...    cache_name="some_cache", query=query_func, tick_type="TRD", db="DEMO_L1",
... )
>>> src = otp.ReadCache("some_cache")
>>> otp.run(src)  
```

Make cache query for specific time interval:

```
>>> src = otp.ReadCache(
...    "some_cache",
...    start=otp.datetime(2024, 1, 1, 12, 30),
...    end=otp.datetime(2024, 1, 2, 18, 0),
... )
>>> otp.run(src)  
```

Override or set otq_params for one query:

```
>>> src = otp.ReadCache(
...    "some_cache",
...    otq_params={"some_param": "test_value"},
... )
>>> otp.run(src)  
```

##### SEE ALSO
**READ_CACHE** OneTick event processor

``onetick.py.create_cache()``

