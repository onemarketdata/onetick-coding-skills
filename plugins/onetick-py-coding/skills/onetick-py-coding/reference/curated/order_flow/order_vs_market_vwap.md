# Were my orders executed above or below the market VWAP (execution performance vs market)

Compare each order's **execution VWAP** (the VWAP of its fills) against the **market VWAP over that
order's lifetime** (arrival → exit). The verdict depends on side: a **buy** is good when it executed
**below** the market VWAP; a **sell** is good when it executed **above**.

Steps:
1. Per order (`group_by='ID'`), compute the execution VWAP from the fills (`PRICE_FILLED`,
   `QTY_FILLED`) and capture the arrival/exit timestamps and the side.
2. For each order, compute the market VWAP over `[ARRIVAL_TIME, EXIT_TIME]` with
   `.join_with_query` — the per-order time window is passed via `start`/`end`.
3. Decide good/bad execution **without branching**: express side as a numeric direction
   `(BUY_FLAG - 0.5) * 2` (→ +1 for buy, -1 for sell) and use a single vectorized comparison.

```python
import onetick.py as otp

orders = otp.DataSource(db='S_ORDERS', tick_type='ORDER')   # use the real db/tick type from context

# 1) per-order execution VWAP + arrival/exit + side
orders = orders.agg(
    {
        'ORDER_VWAP': otp.agg.vwap('PRICE_FILLED', 'QTY_FILLED'),
        'BUY_FLAG': otp.agg.first('BUY_FLAG'),
        'ARRIVAL_TIME': otp.agg.first_time(),
        'EXIT_TIME': otp.agg.last_time(),
    },
    group_by='ID',
)

# 2) market VWAP over each order's [arrival, exit] window
def market_vwap(symbol):
    trades = otp.DataSource(db='US_COMP', tick_type='TRD')   # the relevant market-data db
    return trades.agg({'MARKET_VWAP': otp.agg.vwap('PRICE', 'SIZE')})

orders = orders.join_with_query(
    market_vwap,
    start=orders['ARRIVAL_TIME'],
    end=orders['EXIT_TIME'] + otp.Nano(1),   # +1ns so the exit tick is included
)

# 3) good/bad WITHOUT a per-tick script: direction trick + one comparison.
# direction = +1 for buy, -1 for sell; good when (MARKET_VWAP - ORDER_VWAP) * direction > 0
orders['DIRECTION'] = (orders['BUY_FLAG'] - 0.5) * 2
orders['GOOD_EXECUTION'] = (orders['MARKET_VWAP'] - orders['ORDER_VWAP']) * orders['DIRECTION'] > 0

result = otp.run(orders)
```

Key points:
- Don't reach for `.script` for the buy/sell branch — a simple per-tick conditional is cleaner and
  safer as **vectorized column arithmetic**. The `(BUY_FLAG - 0.5) * 2` direction trick collapses the
  if/else into one expression (`.script` here is easy to mis-write and can fail to compile).
- `.join_with_query` evaluates the inner query **per order** over the window given by `start`/`end`
  taken from the order's own columns (`ARRIVAL_TIME`/`EXIT_TIME`); pass extra per-order values via
  `params=`.
- Execution VWAP uses the fill fields (`PRICE_FILLED`, `QTY_FILLED`); market VWAP uses the trade
  fields (`PRICE`, `SIZE`).
