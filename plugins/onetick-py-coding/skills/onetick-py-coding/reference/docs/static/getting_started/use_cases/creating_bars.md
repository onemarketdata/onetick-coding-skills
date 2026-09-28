# Creating Bars

We create 1-minute bars (bucket_interval=60 seconds) below.

```python
import onetick.py as otp

trd = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD')

trd = trd.character_present(trd['COND'], 'O6TUHILNRWZ47QMBCGPV', discard_on_match=True)

bars = trd.agg({'VOLUME': otp.agg.sum('SIZE'),
                'HIGH': otp.agg.max('PRICE'),
                'LOW': otp.agg.min('PRICE'),
                'OPEN': otp.agg.first('PRICE'),
                'COUNT': otp.agg.count(),
                'CLOSE': otp.agg.last('PRICE')},
                bucket_interval=otp.Minute(1))

otp.run(
    bars,
    symbols=['AAPL'],
    start=otp.dt(2024, 2, 1, 9, 30),
    end=otp.dt(2024, 2, 1, 10),
    timezone='EST5EDT'
)
```


Note: OneTick Cloud has minute bars precomputed and available in \*_BARS databases under the tick type TRD_1M.


Daily OHLCV data with the official closing prices is also available: see `OHLCV`.

Note the use of ``apply_times_daily`` to limit each day’s interval to 9:30-4:00pm (plus one minute is added as the minute bar for 9:30-9:31 has the timestamp of 9:31).

```python
bars = otp.DataSource('US_COMP_BARS', tick_type='TRD_1M')
bars = bars[['FIRST', 'HIGH', 'LOW', 'LAST', 'VOLUME']]
otp.run(
    bars,
    symbols=['AAPL'],
    start=otp.dt(2024, 2, 1, 9, 31),
    end=otp.dt(2024, 2, 1, 16, 1),
    timezone='EST5EDT',
    apply_times_daily=True)
```
