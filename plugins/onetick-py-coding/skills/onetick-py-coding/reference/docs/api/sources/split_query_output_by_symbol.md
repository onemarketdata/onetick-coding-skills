# otp.SplitQueryOutputBySymbol

### ``class SplitQueryOutputBySymbol(query=None, symbol_field=None, single_invocation=False, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, symbols=utils.adaptive, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

A data source used to dispatch output ticks,
resulting after execution of the specified query,
according to the values of the specified field in those ticks.

Each replica of this EP, corresponding to a particular bound or unbound symbol, thus,
propagates resulting ticks of the specified query,
with values of the specified field in those ticks equal to that symbol.

Note, that database name part of symbols is not taken into account.

* **Parameters:**
  * **query** – Specify query to execute.
  * **symbol_field** – Specifies the field in the resulting ticks of the underlying query,
    according to values of which those ticks are dispatched.
  * **single_invocation** – 

    By default, the underlying query is executed once
    per symbol batch and per execution thread of the containing query.
    If this parameter is set to True, the underlying query is executed once
    regardless of the batch size and the number of CPU cores utilized.

    The former option should be the preferred (hence the default) one, as it reduces memory overhead,
    while the latter one might be chosen to speed-up the overall execution.

    Note, that if the underlying query is a CEP query, than this option has no effect,
    as there is a single batch and a single thread anyway.
  * **db**
  * **tick_type** – 

    Tick type to set on the OneTick’s graph node.
    Can be used to specify database name with tick type or tick type only.

    By default setting these parameters is not required, database is usually set
    with parameter `symbols` or in ``otp.run``.
  * **start** – Custom start time of the source.
    If set, will override the value specified in ``otp.run``.
  * **end** – Custom end time of the source.
    If set, will override the value specified in ``otp.run``.
  * **symbols** – Symbol(s) from which data should be taken.
    If set, will override the value specified in ``otp.run``.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Get only the ticks that have needed symbols specified in field `TICKER`:

```
>>> data = otp.Ticks(X=[1, 2, 3, 4], TICKER=['A', 'B', 'A', 'C'])
>>> data = otp.SplitQueryOutputBySymbol(data, data['TICKER'])
>>> res = otp.run(data, symbols=['A', 'B'])
>>> res['A']
                     Time  X TICKER
0 2003-12-01 00:00:00.000  1      A
1 2003-12-01 00:00:00.002  3      A
>>> res['B']
                     Time  X TICKER
0 2003-12-01 00:00:00.001  2      B
```
