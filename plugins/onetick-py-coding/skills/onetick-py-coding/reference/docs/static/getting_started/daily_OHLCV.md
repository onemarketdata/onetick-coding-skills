# Daily OHLCV (with closing prices)

We can retrieve daily OHLCV data for specific tickers using various symbologies, by querying for the `DAY` Table.

For consolidated datasets such as for the US, each exchange’s data and a composite are represented.

The  *\_DAILY* databases such as the sample *US_COMP_SAMPLE_DAILY* are optimized for querying long time periods,
while the Tick and Bar databases are optimized for querying an intraday time range.

Without Symbology:

```python
import onetick.py as otp

data = otp.DataSource(db='US_COMP_SAMPLE_DAILY', tick_type='DAY')
df = otp.run(data,
             start=otp.dt(2024, 1, 1),
             end=otp.dt(2024, 4, 1),
             timezone='America/New_York',
             symbols='CSCO')
```

With Symbology:

```python
data = otp.DataSource(db='US_COMP_SAMPLE_DAILY', tick_type='DAY')
df = otp.run(data,
             start=otp.dt(2024, 1, 1),
             end=otp.dt(2024, 4, 1),
             timezone='America/New_York',
             symbols='BSYM::::CSCO US Equity',
             symbol_date=otp.dt(2024, 1, 3))
df.head(5)
```

For US equities,
`USPRIM` stands for the primary exchange,
empty string for the composite,
and `N` for the New York Stock Exchange.

Most of the time you’ll be looking for composite, which you can specify through a filter:

```python
data = otp.DataSource(db='US_COMP_SAMPLE_DAILY', tick_type='DAY')
data = data[['CLOSE', 'VOLUME', 'EXCHANGE']]
data = data.where(data['EXCHANGE'] == '')
otp.run(data,
        start=otp.dt(2024, 1, 1),
        end=otp.dt(2024, 4, 1),
        timezone='America/New_York',
        symbols='BSYM::::CSCO US Equity',
        symbol_date=otp.dt(2024, 1, 3))
```
