# Time-Based Joins

Time series analysis often involves looking up information from other time series for relevant time ranges. OneTick has built-in functions for this.

# `join_by_time`: Enhancing a time series with information from other time series at the time of each tick

Below we’ll enhance the trades with the prevailing quote (i.e., best bid and ask) at the time of each trade. We’ll first look just at trades, then just at quotes, and finally we’ll join the two.

Let’s examine the trades first.

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
trd = trd[['PRICE', 'SIZE']]
otp.run(trd, start=s, end=e, symbols=['AAPL'])
```

Now let’s take a look at the quotes (or rather the ‘national best bid/offer’).

Note the ``back_to_first_tick`` parameter
which seeks a tick before query start time
and ensures that there is at least one quote at the start of the query
(its actual timestamp is before the start).

It’s commonly used when we want to find the tick (e.g., last quote or trade) at a given time:

```python
qte = otp.DataSource('US_COMP_SAMPLE', tick_type='NBBO', back_to_first_tick=86400)
qte = qte[['BID_PRICE', 'ASK_PRICE']]

given_time = otp.dt(2024, 2, 15, 9, 30)
otp.run(qte, start=given_time, end=given_time, symbols=['AAPL'])
```

Or when we want to make sure there is tick at the start of the query interval
(e.g., to make sure the trades have a prevailing quote even if it’s from before the query start time):

```python
qte = otp.DataSource('US_COMP_SAMPLE', tick_type='NBBO', back_to_first_tick=60)
qte = qte[['BID_PRICE', 'ASK_PRICE']]
otp.run(qte, start=s, end=e, symbols=['AAPL'])
```

We “enhance” the trades with the information from the quotes.

```python
enh_trd = otp.join_by_time([trd, qte])
otp.run(enh_trd, start=s, end=e, symbols=['AAPL'])
```

In other words, each trade is joined with the quote that was active at the time the trade took place. We can examine the quote time to make sure it’s before the trade time.

```python
qte['quote_time'] = qte['Time']
enh_trd = otp.join_by_time([trd, qte])
otp.run(enh_trd, start=s, end=e, symbols=['AAPL'])
```

## Join-by-time Use Cases

`Prevailing quote at the time of a trade`

`Computing Markouts`

# `join_with_query`: Executing a query on each tick

Sometimes we want to do a look up based on the information provided in a tick. For example, we may have a series of order ticks each containing an order arrival and exit times and we may want to find the market vwap during the interval. Let’s take this one step at a time.

Let’s find the market vwap for a given time range (i.e., for a given `start` and `end`).

```python
q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
q = q.agg({'market_vwap': otp.agg.vwap('PRICE', 'SIZE')})
otp.run(q, start=s, end=e, symbols=['AAPL'])
```

Next let’s create a couple of orders each with its own values for `start`/`end` specified in the `arrival`/`exit` columns.

```python
orders = otp.Ticks(arrival=[s, s + otp.Milli(7934)],
                   exit=[e, e + otp.Milli(9556)],
                   sym=['AAPL', 'MSFT'])
otp.run(orders)
```

We can wrap the code that finds vwap into a function and call it for each order while passing the relevant parameters for `start`, `end`, and `symbol`.

```python
def vwap(symbol):
    q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
    q = q.agg({'market_vwap': otp.agg.vwap('PRICE', 'SIZE')})
    return q

orders = otp.Ticks(arrival=[s, s + otp.Milli(7934)],
                   exit=[e, e + otp.Milli(9556)],
                   sym=['AAPL', 'MSFT'])
orders = orders.join_with_query(vwap, start=orders['arrival'], end=orders['exit'], symbol=orders['sym'])
otp.run(orders, start=s, end=s + otp.Day(1))
```



# Interval VWAP: the efficient way

The code above provides an implementation for this use case. However, a more efficient implementation may be useful when the number of orders is large. It appears below.

```python
def vwap(symbol):
    q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
    q = q.agg({'market_vwap': otp.agg.vwap('PRICE','SIZE')})
    return q

orders = otp.Ticks(arrival=[s, s + otp.Milli(7934)],
                   exit=[e, e + otp.Milli(9556)],
                   sym=['AAPL', 'MSFT'])

orders['_PARAM_START_TIME_NANOS'] = orders['arrival']
orders['_PARAM_END_TIME_NANOS'] = orders['exit']
orders['SYMBOL_NAME'] = orders['sym']
```

```python
otp.run(vwap, symbols=orders, date=otp.dt(2024, 2, 1))
```

A separate query is executed for each order in parallel. Each order becomes a `symbol` that specifies the security and the start/end time. The logic in `vwap()` is executed for every (“unbound”) symbol. This is more efficient than calling `join_with_query` as it can be parallelized better. See “Databases, symbols, and tick types” under Concepts for more info.

Note that the start and end parameters are not important for the run method as each of the symbols specifies its own start/end time in `_PARAM_START_TIME_NANOS` and `_PARAM_END_TIME_NANOS`.
