# otp.LoadTicksFromDataFrame

### ``class LoadTicksFromDataFrame(dataframe=None, timestamp_column=utils.adaptive, symbol_name_field=None, symbol=utils.adaptive, db=utils.adaptive_to_default, tick_type=utils.adaptive, query_parameters=None)``

Bases:

Load `pandas.DataFrame` as data source

Relies on ``otp.Ticks``.
So unlike ``onetick.py.ReadFromDataFrame()`` filtering by symbols via symbols parameter
in ``otp.run`` don’t affect output.

Also, it can be used in older OneTick versions.

* **Parameters:**
  * **dataframe** (`pandas.DataFrame`) – Pandas DataFrame to load.
  * **timestamp_column** (*str* *,* *optional*) – 

    Column containing time info.

    If parameter not set and DataFrame has one of columns `TIME` or `Timestamp` (case-insensitive),
    it will be automatically used as `timestamp_column`. To disable this, set `timestamp_column=None`.

    Timestamp column dtype should be either datetime related or string.
  * **symbol_name_field** (*str* *,* *optional*) – Column containing symbol name.
    By default column ‘SYMBOL_NAME’ will be used if it exists.
  * **symbol** (*str*) – 

    Symbol(s) from which data should be taken.

    If both symbol_name_field and symbol are omitted
    ``otp.config.default_symbol`` value will be used.
  * **db** (*str*) – Custom database name for the node of the graph.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

All examples for ``onetick.py.ReadFromDataFrame()`` suitable for this data source.

Let’s look at differences. Here’s ``onetick.py.ReadFromDataFrame()`` output
with `symbols` parameter in ``otp.run``:

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol_name_field='SYMBOL_NAME')  
>>> otp.run(data, date=otp.dt(2024, 1, 1), symbols=['AAA'])  
                     Time SYMBOL_NAME  PRICE
0 2024-01-01 12:00:00.001         AAA  50.05
1 2024-01-01 12:00:02.000         AAA  50.05
2 2024-01-01 12:00:03.100         AAA  49.98
```

Same example for LoadTicksFromDataFrame. As you can see, ticks weren’t filtered by symbol name:

```
>>> dataframe['_SYMBOL'] = dataframe['SYMBOL_NAME']  
>>> src = otp.LoadTicksFromDataFrame(dataframe)  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
                     Time  PRICE _SYMBOL
0 2024-01-01 12:00:00.001  50.05     AAA
1 2024-01-01 12:00:02.000  50.05     AAA
2 2024-01-01 12:00:02.500  49.95     BBB
3 2024-01-01 12:00:03.100  49.98     AAA
4 2024-01-01 12:00:03.250  50.02     BBB
```

##### SEE ALSO
``onetick.py.ReadFromDataFrame()``
