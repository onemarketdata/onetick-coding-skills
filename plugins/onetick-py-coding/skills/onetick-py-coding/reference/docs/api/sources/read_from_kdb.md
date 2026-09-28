# otp.ReadFromKdb

### ``class ReadFromKdb(server_address=None, query=None, symbol_column=None, timestamp_column=None, fields=None, username=None, password=None, symbol=utils.adaptive, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, **kwargs)``

Bases: ``Source``

Retrieves historical or real-time data from KDB databases.

The EP connects to the KDB server, reads data from the specified table or run q queries and
propagates each row as a tick in a time series. When executing running queries with `NOW` as the start time,
it assumes that the data is stored in the real-time database of KDB and EP runs CEP query by subscribing
to the real-time part of data.

* **Parameters:**
  * **server_address** (*str*) – Address of the KDB server. Should have `server_host:port` format.
  * **query** (*str*) – 

    If name of the table is provided then EP will generate **q** query to read data from this table
    for given start/end time of query and for symbols listed in the otq query.
    if **qSQL** query is provided then EP will run this query. EP will replace `$_SYMBOL_NAME` by symbol name
    and `$_START_TIME` and `$_END_TIME` by start and end time of query respectively.

    #### NOTE
    If **qSQL** query is provided, then parameters `symbol_column`, `timestamp_column` and `fields`
    will have no effect.
  * **symbol_column** (*str* *,* *optional*) – 

    Name of column or expression which return symbol name.

    Default value: `sym`.
  * **timestamp_column** (*str* *,* *optional*) – 

    Name of column or expression which return timestamp. Can be expression like date+time
    where `time` is time passed after given date.

    Special timestamp expressions include:
    > * `_AUTO` lets the EP choose the timestamp column automatically when the table has a well-known schema.

    Default value: \_AUTO
  * **fields** (*list* *,* *str* *,* *optional*) – 

    A optional parameter in one the following form:
    * Dictionary with output column name a key and either `None` / empty tuple or column type or tuple with
      column type (one of `supported types`) and column name from KDB database.
      If you want to omit one of them, pass `None` to corresponding tuple part.
    * String in format `FIELD_1 [TYPE_1]=[COLUMN_NAME_1], FIELD_2 [TYPE_2]=[COLUMN_NAME_2], … ,
      FIELD_N [TYPE_N]=[COLUMN_NAME_N]` where `FIELD_K` is name of output field
      which have `TYPE_K` is type and value equal to of value of `COLUMN_NAME_K` column.
      Type specifier and expression are optional. Expressions can depend from columns of KDB table.

    Column types, passed with this parameter will be used to construct schema for source object.

    #### NOTE
    Can’t be set with `query` containing **qSQL query**
  * **username** (*str* *,* *optional*) – Username for authentication on the KDB server. If specified, password parameter must be also specified.
  * **password** (*str* *,* *optional*) – Password for authentication on the KDB server. If specified, username parameter must be also specified.
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **start** (``otp.datetime``) – Start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for tick generation. By default the end time of the query will be used.

##### Examples

Simple query from `some_table`

```
>>> src = otp.ReadFromKdb('kdb-server:5000', 'some_table')  
>>> otp.run(src, symbol='LOCAL::AAPL')                      
                           Time                date            time      price  size   sym
0 2003-12-01 04:42:42.156933546 2003-11-30 19:00:00  34962156933546  68.668341   300  AAPL
1 2003-12-01 08:59:33.463062196 2003-11-30 19:00:00  50373463062196  64.309823    60  AAPL
2 2003-12-01 10:10:40.343438833 2003-11-30 19:00:00  54640343438833  67.087377   530  AAPL
...
```

Rename fields from previous query and keep only them

```
>>> src = otp.ReadFromKdb(
...     'kdb-server:5000', 'some_table',
...     fields={
...         'PRICE': (float, 'price'),
...         'SIZE': (int, 'size')
...     },
... )                                   
>>> otp.run(src, symbol='LOCAL::AAPL')  
                           Time      PRICE  SIZE
0 2003-12-01 04:42:42.156933546  68.668341   300
1 2003-12-01 08:59:33.463062196  64.309823    60
2 2003-12-01 10:10:40.343438833  67.087377   530
...
```

Pass query as `query` parameter instead of table name. In this case filtering by symbol wouldn’t work.

```
>>> src = otp.ReadFromKdb('kdb-server:5000', 'select from some_table')  
>>> otp.run(src, symbol='LOCAL::ANY')                                   
                 Time                date            time      price  size   sym
0 2199-12-31 19:00:00 2003-11-30 19:00:00  12311440987139  65.868242   170  AAPL
1 2199-12-31 19:00:00 2003-11-30 19:00:00  33435228341817  95.989642   248  MSFT
2 2199-12-31 19:00:00 2003-11-30 19:00:00  34962156933546  68.668341   342  AAPL
3 2199-12-31 19:00:00 2003-11-30 19:00:00  50373463062196  64.309823    60  AAPL
...
```

##### SEE ALSO
**READ_FROM_KDB** OneTick event processor
