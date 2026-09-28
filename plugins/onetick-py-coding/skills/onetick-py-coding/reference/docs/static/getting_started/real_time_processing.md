# Real-time processing

All of the queries can run in real-time mode. Real-time processing is enabled via callbacks. We illustrate this on the signal generation use case.



# Use case: Signal Generation

We’ll compute golden cross signals using 50-second and 200-second moving averages

- ‘Entries’ is set to 1 when the short-term moving average goes above the long term (i.e., a signal to buy)
- ‘Exits’ is set to 1 on when the short-term moving average goes below the long term (i.e., a signal to sell)

```python
import onetick.py as otp

trd = otp.DataSource(tick_type='TRD', schema={'PRICE': float})
trd = trd[['PRICE']]

trd = trd.agg({'short': otp.agg.mean('PRICE')}, bucket_interval=60, running=True, all_fields=True)
trd = trd.agg({'long': otp.agg.mean('PRICE')}, bucket_interval=60 * 5, running=True, all_fields=True)

trd['buy'] = (trd['short'][-1] < trd['long'][-1]) & (trd['short'] > trd['long'])
trd['sell'] = (trd['short'][-1] > trd['long'][-1]) & (trd['short'] < trd['long'])
```

We define a callback that for every tick (i.e., on every trade) will

- print a ‘.’ if there is no signal
- print out the tick followed by ‘BUY’ on an entry signal
- print out the tick followed by ‘SELL’ on an exit signal

```python
class GoldenCrossCallback(otp.CallbackBase):
    def process_tick(self, tick, time):
        if not tick['buy'] and not tick['sell']:
            # print('.', end='')
            return
        # print()
        # print()
        print(time, tick)
        if tick['buy']:
            print('BUY')
        if tick['sell']:
            print('SELL')
        print()
```

The query will run continuously with the output printed as the events happen
if the database and start/end times are set accordingly (see the commented out lines).
With the US_COMP_SAMPLE database used and the time interval set in the past, the query will work in “historical” mode.

```python
# timestamps appear in GMT
cb = GoldenCrossCallback()
otp.run(trd,
        callback=cb, running=True,
        symbols='US_COMP_SAMPLE::AMZN',
        start=otp.dt(2024, 2, 1, 10), end=otp.dt(2024, 2, 1, 11),
        # symbols='US_COMP::AMZN',
        # start=otp.dt.now(), end=otp.dt.now() + otp.Day(1),
)
```
