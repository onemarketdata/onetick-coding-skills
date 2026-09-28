# Prevailing quote at the time of a trade

```python
import onetick.py as otp

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
trd = trd[['PRICE', 'SIZE']]

qte = otp.DataSource('US_COMP_SAMPLE',
                     tick_type='NBBO',
                     back_to_first_tick=otp.Minute(10))

qte = qte[['ASK_PRICE', 'BID_PRICE']]
qte['quote_time'] = qte['Time']

enriched_trades = otp.join_by_time([trd, qte])

otp.run(enriched_trades,
        symbols=['AAPL'],
        start=otp.dt(2024, 2, 1, 9, 30),
        end=otp.dt(2024, 2, 1, 9, 30, 1),
        timezone='EST5EDT')
```
