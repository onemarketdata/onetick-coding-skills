# Query intervals; start and end query interval

Every query defines a query interval that lets sources understand where to read ticks

In onetick.py in the `otp.run` a developer could set `start` and `end` parameters, or use the `date` parameter if they want to use entire day

```python
import onetick.py as otp

trades = otp.DataSource(db='US_COMP', tick_type='TRD')

trades = trades.first(n=5)

df = otp.run(trades,
             symbols='AAPL',
             start=otp.dt(2003, 12, 1, 9, 30),
             end=otp.dt(2003, 12, 1, 11),
             timezone='EST5EDT')
```

Sources might have sub-interval, but *they shouldn’t be inside the entire query interval*


The onetick.py allows to specify `start` and` end`, or `date` on a source

```python
import onetick.py as otp

monday = otp.DataSource(db='US_COMP',
                        tick_type='TRD',
                        start=otp.dt(2023, 12, 8, 9, 30),
                        end=otp.dt(2023, 12, 8, 9, 35))

monday = monday[['PRICE']].first().add_prefix('MONDAY_')

friday = otp.DataSource(db='US_COMP',
                        tick_type='TRD',
                        start=otp.dt(2023, 12, 12, 15, 55),
                        end=otp.dt(2023, 12, 12, 16))

friday = friday[['PRICE']].last().add_prefix('FRIDAY_')

data = otp.join(monday, friday, on='same_size')
data['PRICE_CHANGE_pct'] = (data['FRIDAY_PRICE'] - data['MONDAY_PRICE']) * 100 / data['MONDAY_PRICE']

df = otp.run(data,
             symbols='AAPL')
```

You could find in this example that the query range is not set in the `otp.run`, however OneTick expects global query interval set. In that case the onetick.py sets the minimul query interval that covers all sources query interval (ie [9:30 ; 16) in the example) as a query interval. Meantime a developer could clarify it in the `otp.run` additionally, but It should cover other intervals otherwise OneTick rises an error.
