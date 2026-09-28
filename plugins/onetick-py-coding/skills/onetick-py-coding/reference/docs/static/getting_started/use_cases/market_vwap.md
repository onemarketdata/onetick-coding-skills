# Interval Metrics (e.g., VWAP)

```python
import onetick.py as otp

query = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
query = query.agg({'market_vwap': otp.agg.vwap('PRICE', 'SIZE')})

otp.run(
    query,
    symbols=['AAPL'],
    start=otp.dt(2024, 2, 1, 9, 30),
    end=otp.dt(2024, 2, 1, 9, 30, 1),
    timezone='EST5EDT',
)
```

## Computing market VWAP for every order’s arrival/exit interval

```python
start = otp.dt(2024, 2, 1, 9, 30)
end = otp.dt(2024, 2, 1, 9, 30, 1)

orders = otp.Ticks(arrival=[start, start + otp.Milli(7934)],
                   exit=[end, end + otp.Milli(9556)],
                   sym=['AAPL', 'MSFT'])
otp.run(orders, start=start, end=start + otp.Day(1))
```

```python
def vwap(symbol):
    q = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
    q = q.agg({'market_vwap': otp.agg.vwap('PRICE','SIZE')})
    return q

orders = orders.join_with_query(vwap, start=orders['arrival'], end=orders['exit'], symbol=orders['sym'])
otp.run(orders, start=start, end=end + otp.Day(1))
```

A more efficient implementation is also available with `symbol parameters`.
