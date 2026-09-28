# Data Retrieval

OneTick is a time series database meaning that each record has a timestamp and timestamps of consecutive records are non-decreasing. Multiple time series are stored in OneTick. An individual time series is identified by the symbol (aka ticker, financial instrument, security), tick type (i.e., the type of data such as trades or quotes), and the name of the database where the time series is stored.

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

# we can retrieve the list of databases available on the server
# (note that not all databases may be readable by your user,
#  but the _SAMPLE databases should be)
dbs = otp.databases()
list(db for db in dbs if db.endswith('_SAMPLE'))
```

```python
# the list of dates with data for the db
dbs['US_COMP_SAMPLE'].dates()[-5:]
```

```python
# or just the last day with data
dbs['US_COMP_SAMPLE'].last_date
```

```python
# and the list of tick types
dbs['US_COMP_SAMPLE'].tick_types()
```

OneTick Cloud Standard Tables:

| Table    | Description                                                                                   |
|----------|-----------------------------------------------------------------------------------------------|
| DAY      | End of Day Record typically covering Closing Price plus Open Interest for Derivatives Markets |
| IND      | Indicative Prices occurring during Auction phases                                             |
| QTE      | Quote Events                                                                                  |
| STAT     | Static Reference Data for the Instrument                                                      |
| TRD      | Trade Events                                                                                  |
| NBBO     | National Best Bid & Offer Quotes                                                              |
| PRL      | Book Depth - Market By Level                                                                  |
| PRL_FULL | Book Depth - Market by Order                                                                  |
| MKTCAL   | Market Holiday & Trading Hours                                                                |
| TRD_1M   | 1 Minute Trade Bar                                                                            |
| QTE_1M   | 1 Minute Quote Bar                                                                            |

We can now retrieve symbols traded in a given time range. (In many financial markets, there are properties that remain constant throughout the trading day. Examples include the name of a financial instrument and the set of instruments traded).

```python
symbols = otp.Symbols('US_COMP_SAMPLE')
otp.run(symbols, start=otp.dt(2024, 2, 1), end=otp.dt(2024, 2, 2))
```

We used the ``otp.run`` method above,
which executed a query that retrieved the list of symbols.

The start and end timestamps were specified with `onetick-py`’s datetime class
``otp.dt``.

Now that we have database names, tick types, and symbols, we are ready to query a time series.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Note that we specified the start and end of the time series to retrieve the corresponding interval.

#### WARNING
In ``otp.DataSource`` default value of the parameter `schema_policy`
enables automatic deduction of the data schema.

It works fine for simple cases like using `onetick-py` in Jupyter notebooks,
but it is highly not recommended for production code.

For details see `Schema deduction mechanism`.

Let’s just keep the columns we’re interested in to make it more digestible.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

We can retrieve multiple time series.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
mult = otp.run(q, start=s, end=e, symbols=['AAPL', 'MSFT'])
mult
```

Each time series is returned as the value of a dict keyed by the corresponding symbol.

```python
mult['MSFT']
```

We can also retrieve all of the symbols from the database or all of the symbols matching a pattern.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
mult = otp.run(q, start=s, end=e, symbols=otp.Symbols('US_COMP_SAMPLE', pattern='AA%'))
mult
```

We can merge all of the time series by time by using ``otp.merge`` function.

Parameter `identify_input_ts` here automatically adds `SYMBOL_NAME` and `TICK_TYPE` columns
to the output, so each tick can be identified.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
q = otp.merge([q], symbols=['AAPL', 'MSFT'], identify_input_ts=True)
single = otp.run(q, start=s, end=e)
single
```

The time range and symbols can be specified directly on the data source. This way we can have different times ranges for difference sources that we can later merge.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', start=s, end=e, symbols=['AAPL'])
otp.run(q)
```

For example, we can get the data from February 24 for AAPL and from March 20 for MSFT.

```python
aapl = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', start=otp.dt(2024, 2, 24, 9, 30), end=otp.dt(2024, 2, 24, 9, 30, 1), symbols=['AAPL'])
msft = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', start=otp.dt(2024, 3, 20, 9, 30), end=otp.dt(2024, 3, 20, 9, 30, 1), symbols=['MSFT'])
merged = otp.merge([aapl, msft], identify_input_ts=True)
merged = merged[['PRICE', 'SIZE', 'SYMBOL_NAME']]
otp.run(merged)
```

We can also specify multiple symbols in the data source in which case they will be merged by time.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', start=s, end=e, symbols=['AAPL', 'MSFT'])
otp.run(q)
```

We can look up  symbols in multiple databases.

```python
q = otp.DataSource(tick_type='TRD')
q = q.table(PRICE=float, SIZE=int)
q = otp.merge(q, symbols=['US_COMP_SAMPLE::AAPL', 'TDI_FUT_SAMPLE::ES_r_tdi'], identify_input_ts=True)
otp.run(q, start=s, end=e, symbol_date=s)
```

We can also look up the same symbols in different databases (even if they have different tick types).

```python
qte = otp.DataSource('US_COMP_SAMPLE', tick_type='QTE')
qte = qte[['BID_PRICE', 'ASK_PRICE']]
nbbo = otp.DataSource('US_COMP_SAMPLE', tick_type='NBBO')
nbbo = nbbo[['BID_PRICE', 'ASK_PRICE']]

q = otp.merge([qte, nbbo], symbols=['AAPL', 'MSFT'], identify_input_ts=True)
otp.run(q, start=s, end=s + otp.Milli(1))
```

## Generating ticks

There are several ways to generate ticks without accessing the database.
It’s very useful in case you want to check some algorithm fast or to create a test-case.

Classes ``otp.Tick`` and ``otp.Ticks`` can be used for this purpose.

``otp.Tick`` can be used to generate a single tick for the whole time range:

```python
data = otp.Tick(A=1)
data['B'] = 2
otp.run(data)
```

Or to generate ticks with some interval, for example every day:

```python
data = otp.Tick(A=otp.math.rand(1, 100), bucket_interval=1, bucket_units='days')
otp.run(data, start=otp.dt(2024, 2, 1), end=otp.dt(2024, 2, 6))
```

And ``otp.Ticks`` can be used to generate many ticks with different fixed values:

```python
data = otp.Ticks({'A': list(range(5)), 'B': ['c', 'd', 'e', 'f', 'g']})
otp.run(data)
```

``otp.Ticks`` can also be used to generate ticks from `pandas.DataFrame`:

```python
import pandas as pd

df = pd.DataFrame({'Time': [pd.Timestamp(2024, 2, 1, 1, 1, 1), pd.Timestamp(2024, 2, 2, 2, 2, 2)], 'A': [1, 2]})
data = otp.Ticks(df)
otp.run(data, start=otp.dt(2024, 2, 1), end=otp.dt(2024, 2, 6))
```
