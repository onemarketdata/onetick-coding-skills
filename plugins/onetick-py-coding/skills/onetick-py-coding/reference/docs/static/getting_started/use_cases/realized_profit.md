# Realized P&L on the FIFO basis

Realized P&L is computed by keeping track of each buy and sell in the chronological order and updating the total using the oldest matching execution from the opposite side (FIFO). We’ll use the following sequence of trades for illustration.

```python
import onetick.py as otp

trades = otp.Ticks(
    SIDE=['B', 'B', 'B', 'S', 'S', 'S', 'S', 'S', 'B', 'B', 'S'],
    PRICE=[1.0, 2.0, 3.0, 2.5, 4.0, 5.0, 6.0, 7.0, 3.0, 4.0, 1.0],
    SIZE=[700, 20, 570, 600, 100, 100, 100, 100, 150, 10, 100],
)

otp.run(trades)
```

## (New way) Using `pnl_realized` OneTick method

```python
pnl_r = trades.pnl_realized(buy_sell_flag_field='SIDE', output_field_name='PROFIT')
pnl_r = pnl_r.agg({'TOTAL_PROFIT': otp.agg.sum('PROFIT')}, running=True, all_fields=True)
otp.run(pnl_r)
```

## (Old way) Manual implementation

We define the deque variables to keep the buy and sell trades as they arrive. Note that the variables are associated with the trades time series.

```python
trades.state_vars['BUY_DEQUE'] = otp.state.tick_deque()
trades.state_vars['SELL_DEQUE'] = otp.state.tick_deque()
```

As every trade arrives, we apply the following function, which updates the deques and computes the realized profit from the trade.

```python
def fifo_computation_of_realized_profit(tick):
    buy_tick = otp.tick_deque_tick()
    sell_tick = otp.tick_deque_tick()
    tick['PROFIT'] = 0.0

    if tick['SIDE'] == 'B':
        tick.state_vars['BUY_DEQUE'].push_back(tick)
    else:
        tick.state_vars['SELL_DEQUE'].push_back(tick)

    while tick.state_vars['BUY_DEQUE'].get_size() > 0 and tick.state_vars['SELL_DEQUE'].get_size() > 0:
        tick.state_vars['BUY_DEQUE'].get_tick(0, buy_tick)
        tick.state_vars['SELL_DEQUE'].get_tick(0, sell_tick)
        if buy_tick['SIZE'] > sell_tick['SIZE']:
            tick['PROFIT'] += sell_tick['SIZE'] * (sell_tick['PRICE'] - buy_tick['PRICE'])
            buy_tick['SIZE'] -= sell_tick['SIZE']
            sell_tick['SIZE'] = 0
        else:
            tick['PROFIT'] += buy_tick['SIZE'] * (sell_tick['PRICE'] - buy_tick['PRICE'])
            sell_tick['SIZE'] -= buy_tick['SIZE']
            buy_tick['SIZE'] = 0

        if buy_tick['SIZE'] == 0:
            tick.state_vars['BUY_DEQUE'].pop_front()
        if sell_tick['SIZE'] == 0:
            tick.state_vars['SELL_DEQUE'].pop_front()
```

Total realized profit can now be calculated by applying the function to every trade.

```python
trades = trades.script(fifo_computation_of_realized_profit)
trades = trades.agg({'TOTAL_PROFIT': otp.agg.sum('PROFIT')}, running=True, all_fields=True)
otp.run(trades)
```
