# otp.Source.join_with_collection

#### ``Source.join_with_collection(collection_name, query_func=None, how='outer', params=None, start=None, end=None, prefix=None, caching=None, keep_time=None, default_fields_for_outer_join=None)``

For each tick uses `query_func` to join ticks from `collection_name` tick collection
(tick set, unordered tick set, tick list, or tick deque).

* **Parameters:**
  * **collection_name** (*str*) – Name of the collection state variable from which to join ticks. Collections are the following types:
    ``TickSet``,
    ``TickSetUnordered``,
    ``TickList`` and
    ``TickDeque``.
  * **query_func** (*callable*) – 

    Callable `query_func` should return ``Source``. If passed, this query will be used on ticks
    from collection before joining them.
    In this case, `query_func` object will be evaluated by OneTick (not python)
    for every input tick. Note that python code will be executed only once,
    so all python’s conditional expressions will be evaluated only once too.

    Callable should have `source` parameter. When callable is called, this parameter
    will have value of a ``Source`` object representing ticks loaded directly from the collection.
    Any operation applied to this source will be applied to ticks from the collection
    before joining them.

    Also, callable should have the parameters with names
    from `params` if they are specified in this method.

    If `query_func` is not passed, then all ticks from the collection will be joined.
  * **how** ( *'inner'* *,*  *'outer'*) – Type of join. If **inner**, then output tick is propagated
    only if some ticks from the collection were joined to the input tick.
  * **params** (*dict*) – Mapping of the parameters’ names and their values for the `query_func`.
    ``Columns`` can be used as a value.
  * **start** (``otp.datetime``, ``otp.Operation``) – Start time to select ticks from collection.
    If specified, only ticks in collection that have higher or equal timestamp will be processed.
    If not passed, then there will be no lower time bound for the collection ticks.
    This means that even ticks with TIMESTAMP lower than \_START_TIME of the main query will be joined.
  * **end** (``otp.datetime``, ``otp.Operation``) – End time to select ticks from collection.
    If specified, only ticks in collection that have lower timestamp will be processed.
    If not passed, then there will be no upper time bound for the collection ticks.
    This means that even ticks with TIMESTAMP higher than \_END_TIME of the main query will be joined.
  * **prefix** (*str*) – Prefix for the names of joined tick fields.
  * **caching** (*str*) – 

    If None caching is disabled. You can specify caching by using values:
    > * ’per_symbol’: cache is different for each symbol.
  * **keep_time** (*str*) – Name for the joined timestamp column. None means no timestamp column will be joined.
  * **default_fields_for_outer_join** (*dict*) – When you use outer join, all output ticks will have fields from the schema of the joined source.
    If nothing was joined to a particular output tick, these fields will have default values for their type.
    This parameter allows to override the values that would be added to ticks for which nothing was joined.
    Dictionary keys should be field names, and dictionary values should be constants
    or ``Operation`` expressions
  * **self** (*Source*)
* **Returns:**
  Source with joined ticks from `collection_name`
* **Return type:**
  ``Source``

##### Examples

```
>>> src = otp.Tick(A=1)
>>> src.state_vars['TICK_SET'] = otp.state.tick_set('LATEST_TICK', 'B', otp.eval(otp.Tick(B=1, C='STR')))
>>> src = src.join_with_collection('TICK_SET')
>>> otp.run(src)[["A", "B", "C"]]
    A  B    C
0  1  1  STR
```

```
>>> src = otp.Ticks(A=[1, 2, 3, 4, 5],
...                 B=[2, 2, 3, 3, 3])
>>> src.state_vars['TICK_LIST'] = otp.state.tick_list()
>>> def fun(tick): tick.state_vars['TICK_LIST'].push_back(tick)
>>> src = src.script(fun)
>>> def join_fun(source, param_b):
...     source = source.agg(dict(VALUE=otp.agg.sum(source['A'])))
...     source['VALUE'] = source['VALUE'] + param_b
...     return source
>>> src = src.join_with_collection('TICK_LIST', join_fun, params=dict(param_b=src['B']))
>>> otp.run(src)[["A", "B", "VALUE"]]
    A  B  VALUE
0  1  2      3
1  2  2      5
2  3  3      9
3  4  3     13
4  5  3     18
```

Join last standing quote from each exchange to trades:

```
>>> trd = otp.Ticks(offset=[1000, 2000, 3000, 4000, 5000],
...                 PRICE=[10.1, 10.2, 10.15, 10.23, 10.4],
...                 SIZE=[100, 50, 100, 60, 200])
>>> qte = otp.Ticks(offset=[500, 600, 1200, 2500, 3500, 3600, 4800],
...                 EXCHANGE=['N', 'C', 'Q', 'Q', 'C', 'N', 'C'],
...                 ASK_PRICE=[10.2, 10.18, 10.18, 10.15, 10.31, 10.32, 10.44],
...                 BID_PRICE=[10.1, 10.17, 10.17, 10.1, 10.23, 10.31, 10.4])
>>> trd['TICK_TYPE'] = 'TRD'
>>> qte['TICK_TYPE'] = 'QTE'
>>> trd_qte = trd + qte
>>> trd_qte.state_vars['LAST_QUOTE_PER_EXCHANGE'] = otp.state.tick_set(
...     'LATEST', 'EXCHANGE',
...     schema=['EXCHANGE', 'ASK_PRICE', 'BID_PRICE'])
>>> trd_qte = trd_qte.state_vars['LAST_QUOTE_PER_EXCHANGE'].update(where=trd_qte['TICK_TYPE'] == 'QTE',
...                                                                value_fields=['ASK_PRICE', 'BID_PRICE'])
>>> trd = trd_qte.where(trd_qte['TICK_TYPE'] == 'TRD')
>>> trd.drop(['ASK_PRICE', 'BID_PRICE', 'EXCHANGE'], inplace=True)
>>> trd = trd.join_with_collection('LAST_QUOTE_PER_EXCHANGE')
>>> otp.run(trd)[['PRICE', 'SIZE', 'EXCHANGE', 'ASK_PRICE', 'BID_PRICE']]
    PRICE  SIZE EXCHANGE  ASK_PRICE  BID_PRICE
0   10.10   100        N      10.20      10.10
1   10.10   100        C      10.18      10.17
2   10.20    50        N      10.20      10.10
3   10.20    50        C      10.18      10.17
4   10.20    50        Q      10.18      10.17
5   10.15   100        N      10.20      10.10
6   10.15   100        C      10.18      10.17
7   10.15   100        Q      10.15      10.10
8   10.23    60        N      10.32      10.31
9   10.23    60        C      10.31      10.23
10  10.23    60        Q      10.15      10.10
11  10.40   200        N      10.32      10.31
12  10.40   200        C      10.44      10.40
13  10.40   200        Q      10.15      10.10
```

##### SEE ALSO
**JOIN_WITH_COLLECTION_SUMMARY** OneTick event processor
