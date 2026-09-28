# otp.SqlQuery

### ``class SqlQuery(sql_statement, merge_all_symbols=False, separate_dbname=False)``

Bases: `SqlQuery`

Constructs SQL query object.

* **Parameters:**
  * **sql_statement** (*str*) – The SQL statement string.
  * **merge_all_symbols** (*bool*) – If set to True, ticks returned by the query for all symbols get merged into a single time series.
  * **separate_dbname** (*bool*) – If set to True, and `merge_all_symbols` is set to True,
    *SYMBOL_NAME* field contains a symbol name without the database name,
    and *DB_NAME* field contains the database name for a symbol.

##### Examples

Select two fields from a single tick type and symbol and return first three ticks from a single day:

```
>>> otp.run(  
...     otp.SqlQuery(
...         "select PRICE,SIZE from US_COMP_SAMPLE.TRD"
...         " where symbol_name = 'AAPL'"
...         " and start_time = '2024-02-01 00:00:00 EST5EDT' and end_time = '2024-02-02 00:00:00 EST5EDT'"
...         " limit 3"
...     ),
... )
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.008283417  186.50     6
1 2024-02-01 04:00:00.008290927  185.59     1
2 2024-02-01 04:00:00.008291153  185.49   107
```

Join quotes and trades:

```
>>> otp.run(  
...     otp.SqlQuery(
...         "select t.PRICE,q.ASK_PRICE,q.BID_PRICE"
...         " from US_COMP_SAMPLE.TRD t join US_COMP_SAMPLE.QTE q"
...         " on sametime_as_existing(t.timestamp, q.timestamp, 0) = TRUE"
...         " where t.symbol_name = 'AAPL' and q.symbol_name = 'AAPL'"
...         " and start_time = '2024-02-01 00:00:00 EST5EDT' and end_time = '2024-02-02 00:00:00 EST5EDT'"
...         " limit 2"
...     ),
... )
                           Time  T.PRICE  Q.ASK_PRICE  Q.BID_PRICE
0 2024-02-01 04:00:00.008283417   186.50       187.02       185.49
1 2024-02-01 04:00:00.008290927   185.59       187.02       185.49
```

Calculate average price of trades across several symbols:

```
>>> otp.run(  
...     otp.SqlQuery(
...         "select COUNT(*) as COUNT, AVG(PRICE) as AVG_PRICE"
...         " from US_COMP_SAMPLE.TRD"
...         " where symbol_name in ('AAPL', 'AAL')"
...         " and start_time = '2024-02-01 00:00:00 EST5EDT' and end_time = '2024-02-02 00:00:00 EST5EDT'",
...         merge_all_symbols=True
...     ),
... )
        Time     COUNT   AVG_PRICE
0 2024-02-02  921779.0  166.697838
```

##### SEE ALSO
``otp.run``
