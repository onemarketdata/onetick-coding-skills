# Retrieving Tick Data

```python
import onetick.py as otp

s = otp.dt(2024, 2, 1, 9, 30)
e = otp.dt(2024, 2, 1, 9, 30, 1)
trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')
otp.run(trd, start=s, end=e, symbols=['AAPL'], timezone='EST5EDT')
```
