# Selecting time series

## Bound and unbound symbols

OneTick provides two ways of specifing symbols / tickers:

- **unbound symbols** – a symbol or symbols for every source in a query that doesn’t have symbol
- **bound symbols** – a symbol or symbols attached to a concrete source

### Unbound symbols

Unbound symbols in onetick.py are symbols passed into the otp.run call

```python
import onetick.py as otp

trades = otp.DataSource(db='DEMO_L1', tick_type='TRD')
trades = trades[['PRICE']]

quotes = otp.DataSource(db='DEMO_L1', tick_type='QTE')
quotes = quotes[['ASK_PRICE', 'BID_PRICE']]

data = otp.join_by_time([trades, quotes])

data = data.agg({'COUNT': otp.agg.count()})

# NOTE: a list of symbols returns dict of dataframes
dfs = otp.run(data,
              symbols=['DEMO_L1::AAPL', 'DEMO_L1::A'],  # <--- list of unbound symbols
              date=otp.dt(2003, 12, 1),
              timezone='EST5EDT')
```

### Bound symbols

The following example shows how to set bound symbols.
Import thing in this example that ubound symbols are not set; we will discuss it a bit later.

```python
import onetick.py as otp

# AAPL here is bound
trades = otp.DataSource(db='DEMO_L1', tick_type='TRD', symbol='AAPL')
trades = trades[['PRICE']]

# AAPL here is bound
quotes = otp.DataSource(db='DEMO_L1', tick_type='QTE', symbol='AAPL')
quotes = quotes[['ASK_PRICE', 'BID_PRICE']]

data = otp.join_by_time([trades, quotes])

# no unbound symbol
otp.run(data,
        date=otp.dt(2003, 12, 1),
        timezone='EST5EDT')
```

### Mixed symbols

It’s possible to use unbound and bound symbols together.
In that case a query runs for every unbound symbol, and that unbound symbols is applied for every source where is no symbols defined.

```python
import onetick.py as otp

# AAI symbol is bound here
aai_trades = otp.DataSource(db='DEMO_L1', tick_type='TRD', symbol='AAI')
aai_trades = aai_trades[['PRICE', 'SIZE']]
aai_trades = aai_trades.agg({'AAI_VWAP': otp.agg.vwap('PRICE', 'SIZE')}, bucket_interval=60)

# no symbols specified here
other_trades = otp.DataSource(db='DEMO_L1', tick_type='TRD')
other_trades = other_trades[['PRICE', 'SIZE']]
other_trades = other_trades.agg({'OTHER_VWAP': otp.agg.vwap('PRICE', 'SIZE')}, bucket_interval=60)

data = otp.join(aai_trades, other_trades, on='same_size')

data = data.agg({'CORRELATION': otp.agg.correlation('AAI_VWAP', 'OTHER_VWAP')})

# unbound symbols are here
dfs = otp.run(
        data,
        symbols=['DEMO_L1::A', 'DEMO_L1::ABAX'],
        date=otp.dt(2003, 12, 1))

# print correlation pairs
for key, value in dfs.items():
    print(f'Correlation AAI vs {key}: ')
    print(value)
```

### Limitations

OneTick defines following rules for symbols.

The first rule: every source should have at least on symbol that either specified as bound or covered by unbound symbols.
If not then a developer gets the No symbol name is specified for at least one branch error message

In this example symbols for quotes are not defined – neither bound nor unbound

```python
import onetick.py as otp

trades = otp.DataSource(db='DEMO_L1', tick_type='TRD', symbol='AAPL')
trades = trades[['PRICE']]

quotes = otp.DataSource(db='DEMO_L1', tick_type='QTE')
quotes = quotes[['ASK_PRICE', 'BID_PRICE']]

data = otp.join_by_time([trades, quotes])

otp.run(data, date=otp.dt(2003, 12, 1))
```

The second rule: There can’t be set bound and unbound symbols simultaniously for any of source.
If not then a developer gets the Query graph binds all sources to specific symbol names error.

In the following example two sources have bound symbols, however the otp.run also specified symbols, and OneTick doesn’t understand where this unbound symbol chould be assigned and raises the error

```python
import onetick.py as otp

trades_1 = otp.DataSource(db='DEMO_L1', tick_type='TRD', symbol='AAPL')
trades_1 = trades_1[['PRICE']]

trades_2 = otp.DataSource(db='DEMO_L1', tick_type='TRD', symbol='ABAX')
trades_2 = trades_2[['PRICE']]

data = otp.join_by_time([trades_1.add_prefix('AAPL_'), trades_2.add_prefix('ABAX_')])

otp.run(data,
        symbols='DEMO_L1::A',
        date=otp.dt(2003, 12, 1))
```

### Current symbol

A developer could get current symbol in any operations

In onetick.py every source has the .Symbol property that has the .name property that points to current symbol

```python
# create a new field `SN` and put there current symbol name
data['SN'] = data.Symbol.name
```
