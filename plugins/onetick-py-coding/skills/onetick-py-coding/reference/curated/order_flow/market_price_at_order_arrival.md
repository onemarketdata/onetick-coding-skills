# What was the market (trade) price at the time an order was placed / at order arrival

An order's own ticks do **not** carry the market trade price — the order `PRICE` field is the order's
limit/quote price, not the price trading in the market. To answer "what was the market/trade price
when this order was placed", you must look it up in the **market-data database** at the order's
**arrival** (`STATE='N'`).

You don't have to know the market-data database and symbol up front: order ticks carry `MD_DB` (the
market-data database name) and `MD_SYMBOL` (the instrument symbol in that database). Use a
multistage (First Stage Query) pattern — the FSQ produces, per order symbol, the `MD_DB`/`MD_SYMBOL`,
and the main query reads trades from exactly that database/symbol and takes the prevailing trade at
the arrival timestamp.

```python
import onetick.py as otp

ORDERS_DB = 'S_ORDERS'   # use the actual orders db / tick type from the context
ORDER_ID = 'A111'        # the order you care about

def first_stage_query() -> otp.Source:
    # one row per order symbol, exposing the market-data db + symbol for that order
    orders = otp.DataSource(db=ORDERS_DB, tick_type='ORDER',
                            schema={'MD_DB': str, 'MD_SYMBOL': str})
    orders = orders.first()
    orders['SYMBOL_NAME'] = orders.Symbol.name   # FSQ must define SYMBOL_NAME
    return otp.merge(orders, symbols=otp.Symbols(db=ORDERS_DB))

def main_query(symbol) -> otp.Source:
    # the order's arrival tick (STATE='N') — one tick, carrying the arrival TIMESTAMP
    arrival = otp.DataSource(db=ORDERS_DB, tick_type='ORDER')
    arrival, _ = arrival[arrival['ID'] == ORDER_ID]
    arrival, _ = arrival[arrival['STATE'] == 'N']

    # the market trades for THIS order's instrument, from its own market-data db/symbol;
    # back_to_first_tick lets us pick up the prevailing trade if none is exactly at arrival
    trades = otp.DataSource(db=symbol['MD_DB'], symbol=symbol['MD_SYMBOL'],
                            tick_type='TRD', back_to_first_tick=otp.Day(1))

    # as-of join: attach the prevailing trade (<= arrival time) to the arrival tick.
    # arrival is the leading source, so its timestamp drives the join.
    return otp.join_by_time([arrival, trades])

result = otp.run(otp.merge(main_query, symbols=first_stage_query()))
```

Key points:
- Don't read a "market price" off the order — there isn't one. Join to the market-data database.
- `MD_DB` / `MD_SYMBOL` identify that database/symbol per order; they always exist and are populated.
- `STATE='N'` is the arrival; use its timestamp to pick the prevailing market trade.
- `back_to_first_tick` (or an as-of `join_by_time`) handles the common case where there is no trade
  exactly at the arrival instant — you want the latest trade at or before it.
