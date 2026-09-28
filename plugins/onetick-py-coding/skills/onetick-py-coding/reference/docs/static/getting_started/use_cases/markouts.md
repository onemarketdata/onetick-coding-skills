# Point-in-time benchmarks: BBO at different markouts

Find the prevailing quote at different time intervals (markouts) before/after each trade.

```python
import onetick.py as otp

markouts = [-1, 0, 1, 5, 60, 600]

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
trd = trd[['PRICE', 'SIZE']]

qte_by_markout = []
for m in markouts:
    mr = str(m).replace('-', 'm')
    qte = otp.DataSource('US_COMP_SAMPLE',
                         tick_type='NBBO',
                         back_to_first_tick=otp.Hour(24))
    qte = qte[['ASK_PRICE', 'BID_PRICE']]
    qte = qte.rename({'ASK_PRICE': f'ASK_PRICE_{mr}',
                      'BID_PRICE': f'BID_PRICE_{mr}'})
    qte[f'quote_time_{mr}'] = qte['Time']

    # shift the data by m seconds
    qte = qte.time_interval_shift(m * 1000)
    qte_by_markout.append(qte)

trd = otp.join_by_time([trd] + qte_by_markout)
otp.run(
    trd,
    symbols=['AAPL'],
    start=otp.dt(2024, 2, 1, 9, 30),
    end=otp.dt(2024, 2, 1, 9, 30, 1),
    timezone='EST5EDT',
)
```
