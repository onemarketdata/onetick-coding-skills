# Variables and Data Structures

## ``Variables (aka ‘state variables’)``

Variables can be used to keep track of state across ticks. The example below shows how we may keep track of P&L.

```python
import onetick.py as otp
s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

trd = otp.Ticks({'PRICE': [13.5, 13.6, 13.3, 14.0],
                 'SIZE': [200, 100, 150, 200],
                 'SIDE': ['B', 'S', 'B', 'S']})

trd.state_vars['PROFIT'] = 0
trd.state_vars['PROFIT'] += trd.apply(
    lambda t: t['PRICE'] * t['SIZE'] if t['SIDE'] == 'S' else -(t['PRICE'] * t['SIZE'])
)

trd['PROFIT'] = trd.state_vars['PROFIT']

otp.run(trd)
```

The variable ‘PROFIT’ keeps a running total. In other words, it aggregates state across trades.

Note that the same can be accomplished without variables by keeping the running total in a separate column.

```python
trd = otp.Ticks({'PRICE': [13.5, 13.6, 13.3, 14.0],
                 'SIZE': [200, 100, 150, 200],
                 'SIDE': ['B', 'S', 'B', 'S']})
trd['VALUE'] = trd.apply(
    lambda t: t['PRICE'] * t['SIZE'] if t['SIDE'] == 'S' else -(t['PRICE'] * t['SIZE'])
)
trd = trd.agg({'PROFIT': otp.agg.sum('VALUE')}, running=True)
otp.run(trd)
```

Below is an example of using variables that cannot be as easily implemented with running totals: remembering the value of the last tick and referring to it after grouping/aggregation.

```python
q = otp.Ticks(X=[-1, 3, -3, 4, 2], Y=[0, 1, 1, 0, 3])
q.state_vars['last_x'] = 0
q.state_vars['last_x'] = q['X']
q = q.high('X', group_by=['Y'])
q['last_x'] = q.state_vars['last_x']
otp.run(q)
```

# Dictionaries / Maps (aka `tick sets`)

## Looking up static data for every tick

A map can be created with keys taken from one or more columns and holding entire ticks as values.

The example below uses exchange reference data to create a map keyed by exchange code.

```python
exchanges = otp.Ticks(
    EXCHANGE=['A', 'B', 'C', 'D', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'P', 'Q', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'],
    NAME=['NYSE American (Amex)', 'Nasdaq BX', 'NYSE National (NSX)', 'FINRA ADF + NYSE/Nasdaq TRFs', 'MIAX Pearl', 'International Securities Exchange', 'Cboe EDGA', 'Cboe EDGX', 'Long-Term Stock Exchange (LTSE)', 'NYSE Chicago', 'New York Stock Exchange', 'NYSE Arca', 'Nasdaq (Tape C securities)', 'Consolidated Tape System (CTS)', 'Nasdaq (Tape A,B securities)', 'Members Exchange (MEMX)', "The Investors' Exchange (IEX)", 'CBOE Stock Exchange (CBSX)', 'Nasdaq PSX', 'Cboe BYX', 'Cboe BZX']
)
exchanges['LOCATION'] = 'US'
otp.run(exchanges)
```

We will add exchange name to trades. First let’s examine the trades.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

The value of the `EXCHANGE` field from trades will be used to look up the name of the corresponding exchange.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE','SIZE','COND','EXCHANGE']]
exchanges = otp.Ticks(
    EXCHANGE=['A', 'B', 'C', 'D', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'P', 'Q', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z'],
    NAME=['NYSE American (Amex)', 'Nasdaq BX', 'NYSE National (NSX)', 'FINRA ADF + NYSE/Nasdaq TRFs', 'MIAX Pearl', 'International Securities Exchange', 'Cboe EDGA', 'Cboe EDGX', 'Long-Term Stock Exchange (LTSE)', 'NYSE Chicago', 'New York Stock Exchange', 'NYSE Arca', 'Nasdaq (Tape C securities)', 'Consolidated Tape System (CTS)', 'Nasdaq (Tape A,B securities)', 'Members Exchange (MEMX)', "The Investors' Exchange (IEX)", 'CBOE Stock Exchange (CBSX)', 'Nasdaq PSX', 'Cboe BYX', 'Cboe BZX']
)

q.state_vars['exchanges'] = otp.state.tick_set('latest', 'EXCHANGE', otp.eval(exchanges))
q['exchange_name'] = q.state_vars['exchanges'].find('NAME', 'unknown')

otp.run(q, start=s, end=e, symbols=['AAPL'])
```

## Checking if ticks are present in another time series

A tick set can be used to check if (a small number of) ticks are present in another time series with potentially different time stamps.

We generate two “special” ticks.

```python
ticks = otp.Ticks(PRICE=[412.22, 400], SIZE=[247, 1000], INDEX=[1, 2])
otp.run(ticks)
```

We then check if any of the trades match these ticks by `PRICE` and `SIZE`.

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q[['PRICE', 'SIZE', 'COND', 'EXCHANGE']]

q.state_vars['special_ticks'] = otp.state.tick_set('latest', ['PRICE', 'SIZE'], otp.eval(ticks))
q['special'] = q.state_vars['special_ticks'].find('INDEX', -1)

q = q.where(q['special'] != -1)

otp.run(q, start=s, end=e, symbols=['AAPL'])
```

# Lists and Queues

Lists and double-ended queues (deques) can be used to keep track of collections ticks.

## Lists and Queues Use Cases

`Realized P&L (FIFO)`
