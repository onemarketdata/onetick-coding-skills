# otp.eval

### ``eval(query, symbol=None, start=None, end=None, generate_separate_file_only=False, continuous=None, **kwargs)``

Creates an object with `query` with saved parameters that can be used later.

It can be used to:

* return a list of symbols for which the main query
  will be executed (multistage queries).
  Note that in this case `query` must return ticks with column **SYMBOL_NAME**.
* return some value dynamically to be used in other places in the main query.
  Note that in this case `query` must return only single tick.

Note that only constant expressions are allowed in query parameters,
they must not depend on ticks.

* **Parameters:**
  * **query** (``onetick.py.Source``, ``onetick.py.query`` or function) – source or query to evaluate.
    If function, then it must return ``onetick.py.Source`` or ``onetick.py.query``.
    Parameter with name **symbol** and parameters specified in `kwargs` will be propagated
    to this function. Parameter from `kwargs` *must* be specified in function signature,
    but parameter **symbol** may be omitted if it is not used.
  * **symbol** (``_SymbolParamSource``) – symbol parameter that will be used by `query` as a symbol.
    If the function is used as a `query`, parameter **symbol** can be defined in function
    signature and used in source operations.
  * **start** (meta field (``MetaFields``)            or symbol param (``_SymbolParamColumn``)) – start time with which `query` will be executed.
    By default the start time for evaluated query is inherited from the main query.
  * **end** (meta field (``MetaFields``)          or symbol param (``_SymbolParamColumn``)) – end time with which `query` will be executed.
    By default the end time for evaluated query is inherited from the main query.
  * **generate_separate_file_only** (*bool*) – If set, sub-query will be generated in separate file.
    It’s needed in some cases, e.g. when generating query for *otq_query_loader_daily.exe*,
    which executes all queries from a file.
  * **continuous** (*bool*) – Must be set to True to be able to run the evaluated query in CEP mode.
  * **kwargs** (str, int, meta fields (``MetaFields``)             or symbol params (``_SymbolParamColumn``)) – or `join_with_query()` parameters
    parameters that will be passed to `query`.
    If the function is used as a `query`, parameters specified in `kwargs`
    *must* be defined in function signature and can be used in source operations.

##### Examples

Use `otp.eval` to be passed as symbols when running the query:

```
>>> def fsq():
...     symbols = otp.Ticks(SYMBOL_NAME=['AAPL', 'AAL'])
...     return symbols
>>> main = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', date=otp.dt(2024, 2, 1))
>>> main = main[['PRICE', 'SIZE']][:3]
>>> main['SYMBOL_NAME'] = main.Symbol.name
>>> main = otp.merge([main], symbols=otp.eval(fsq))
>>> otp.run(main)
                           Time   PRICE  SIZE SYMBOL_NAME
0 2024-02-01 04:00:00.008283417  186.50     6        AAPL
1 2024-02-01 04:00:00.008290927  185.59     1        AAPL
2 2024-02-01 04:00:00.008291153  185.49   107        AAPL
3 2024-02-01 04:00:00.097381367   14.33     1         AAL
4 2024-02-01 04:00:00.138908789   14.37     1         AAL
5 2024-02-01 04:00:00.726613365   14.36    10         AAL
```

Use `otp.eval` as filter:

```
>>> def get_filter(a, b):
...     return otp.Tick(WHERE=f'X >= {str(a)} and X < {str(b)}', OTHER_FIELD='X')
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data = data.where(otp.eval(get_filter, a=0, b=2)['WHERE'])
>>> otp.run(data)
        Time  X
0 2003-12-01  1
```

Use `otp.eval` with meta fields:

```
>>> def filter_by_tt(tick_type):
...     res = otp.Ticks({
...         'TICK_TYPE': ['TRD', 'QTE'],
...         'WHERE': ['PRICE>=190', 'ASK_PRICE>=180']
...     })
...     res = res.where(res['TICK_TYPE'] == tick_type)
...     return res.drop(['TICK_TYPE'])
>>> t = otp.DataSource('US_COMP_SAMPLE::TRD')
>>> t = t[['PRICE', 'SIZE']]
>>> t = t.where(otp.eval(filter_by_tt, tick_type=t['_TICK_TYPE']))
>>> otp.run(t, date=otp.dt(2024, 2, 1))  
                              Time   PRICE  SIZE
0    2024-02-01 10:53:56.130163522  192.42     6
1    2024-02-01 12:58:30.442440693  192.60   200
2    2024-02-01 14:44:08.734358075  190.94    48
3    2024-02-01 16:02:02.421242609  196.09    10
4    2024-02-01 16:30:00.030074464  190.00    11
...                            ...     ...   ...
```

##### SEE ALSO
`Symbol Parameters Objects`
`Symbol parameters`
