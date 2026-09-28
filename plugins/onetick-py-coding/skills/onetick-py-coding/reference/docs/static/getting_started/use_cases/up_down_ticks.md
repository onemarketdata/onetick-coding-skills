# Upticks / Downticks

Let’s mark each trade as an uptick if its price is above the last trade’s price and as a downtick if it’s below.

```python
import onetick.py as otp

def uptick(t):
    if t['PRICE'] == otp.nan or t['PRICE'][-1] == otp.nan:
        return otp.nan
    if t['PRICE'] > t['PRICE'][-1]:
        return 1
    elif t['PRICE'] < t['PRICE'][-1]:
        return -1
    else:
        return 0

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
trd = trd[['PRICE']]
trd['UPTICK'] = trd.apply(uptick)
otp.run(
    trd,
    symbols=['AAPL'],
    start=otp.dt(2024, 2, 1, 9, 30),
    end=otp.dt(2024, 2, 1, 9, 30, 1),
)
```
