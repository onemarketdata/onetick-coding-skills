# otp.ReadFromDataFrame

### ``class ReadFromDataFrame(dataframe=None, timestamp_column=utils.adaptive, symbol_name_field=None, symbol=utils.adaptive, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, query_parameters=None, **kwargs)``

Bases:

Load `pandas.DataFrame` as data source

* **Parameters:**
  * **dataframe** (`pandas.DataFrame`) – Pandas DataFrame to load.
  * **timestamp_column** (*str* *,* *optional*) – 

    Column containing time info.

    If parameter not set and DataFrame has one of columns `TIME` or `Timestamp` (case-insensitive),
    it will be automatically used as `timestamp_column`. To disable this, set `timestamp_column=None`.

    Timestamp column dtype should be either datetime related or string.
  * **symbol_name_field** (*str* *,* *optional*) – Column containing symbol name.
  * **symbol** (*str*) – 

    Symbol(s) from which data should be taken.

    If both symbol_name_field and symbol are omitted
    ``otp.config.default_symbol`` value will be used.
  * **db** (*str*) – Custom database name for the node of the graph.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **start** (``otp.datetime``) – Custom start time of the query.
  * **end** (``otp.datetime``) – Custom end time of the query.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Let’s assume that we have the following pandas dataframe:

```
>>> print(dataframe)  
                 Timestamp  SIDE  PRICE  SIZE
0  2024-01-01 12:00:00.001   BUY  50.05   100
1  2024-01-01 12:00:02.000  SELL  50.05   150
2  2024-01-01 12:00:02.500   BUY  49.95   200
3  2024-01-01 12:00:03.100  SELL  49.98    80
4  2024-01-01 12:00:03.250   BUY  50.02   250
```

Simple dataframe loading, timestamp column will be automatically detected and converted to datetime:

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol='AAPL')  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
                     Time  SIDE  PRICE  SIZE SYMBOL_NAME
0 2024-01-01 12:00:00.001   BUY  50.05   100        AAPL
1 2024-01-01 12:00:02.000  SELL  50.05   150        AAPL
2 2024-01-01 12:00:02.500   BUY  49.95   200        AAPL
3 2024-01-01 12:00:03.100  SELL  49.98    80        AAPL
4 2024-01-01 12:00:03.250   BUY  50.02   250        AAPL
```

Setting custom timestamp_column. For example, if we have `DATA_TIMES` column, instead of `Timestamps`

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol='AAPL', timestamp_column='DATA_TIMES')  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
                     Time  SIDE  PRICE  SIZE SYMBOL_NAME
0 2024-01-01 12:00:00.001   BUY  50.05   100        AAPL
1 2024-01-01 12:00:02.000  SELL  50.05   150        AAPL
2 2024-01-01 12:00:02.500   BUY  49.95   200        AAPL
3 2024-01-01 12:00:03.100  SELL  49.98    80        AAPL
4 2024-01-01 12:00:03.250   BUY  50.02   250        AAPL
```

You can load data even without time data. `Time` column will be set as query end time.

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol='AAPL')  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
        Time  SIDE  PRICE  SIZE SYMBOL_NAME
0 2024-01-02   BUY  50.05   100        AAPL
1 2024-01-02  SELL  50.05   150        AAPL
2 2024-01-02   BUY  49.95   200        AAPL
3 2024-01-02  SELL  49.98    80        AAPL
4 2024-01-02   BUY  50.02   250        AAPL
```

Same effect will be if you don’t set `timestamp_column` and disable automatic timestamp detection:

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol='AAPL', timestamp_column=None)  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
        Time                Timestamp  SIDE  PRICE  SIZE SYMBOL_NAME
0 2024-01-02  2024-01-01 12:00:00.001   BUY  50.05   100        AAPL
1 2024-01-02  2024-01-01 12:00:02.000  SELL  50.05   150        AAPL
2 2024-01-02  2024-01-01 12:00:02.500   BUY  49.95   200        AAPL
3 2024-01-02  2024-01-01 12:00:03.100  SELL  49.98    80        AAPL
4 2024-01-02  2024-01-01 12:00:03.250   BUY  50.02   250        AAPL
```

Setting `symbol_name_field` for setting symbol name from dataframe.
In this example, let’s say, that we have column `SYMBOL` with symbol names.

```
>>> src = otp.ReadFromDataFrame(dataframe, symbol_name_field='SYMBOL')  
>>> otp.run(src, date=otp.date(2024, 1, 1))  
                     Time  SIDE  PRICE  SIZE SYMBOL
0 2024-01-01 12:00:00.001   BUY  50.05   100   AAPL
1 2024-01-01 12:00:02.000  SELL  50.05   150   AAPL
2 2024-01-01 12:00:02.500   BUY  49.95   200   AAPL
3 2024-01-01 12:00:03.100  SELL  49.98    80   AAPL
4 2024-01-01 12:00:03.250   BUY  50.02   250   AAPL
```

##### SEE ALSO
``onetick.py.LoadTicksFromDataFrame()``
