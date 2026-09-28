# Trades

Trades represent ordered events (ticks) of deals on the venue / exchange or just collected together (consolidated) in a database.

We store trades in OneTick database under the 'TRD' tick type.

# a trade tick schema

Trades has usually the following schema in OneTick database

- `PRICE` double - price of an executed trade
- `SIZE` long - quantity of an executed trade

# Creating trade ticks with otp.Ticks

This example creates two sequential trade ticks with timestamps +10 ms from the query start for the first tick and +1000 ms from the query start. The first trade has price 100.0 when the second has 101.0. The have the same quantity.

```python
import onetick.py as otp

trades = otp.Ticks({
        "offset": [10, 1000],
        "PRICE": [100.0, 101.0],
        "SIZE": [10] * 2,
})
```

# trades volume

The following code illustrate how calculating trades volume can be expressed in the `onetick.py`

```python
import onetick.py as otp

trades = otp.DataSource(
    db='US_COMP',
    tick_type='TRD',
    symbol='AAPL',
    date=otp.dt(27, 11, 2024)
)

volume_src = trades.agg({'VOLUME': otp.agg.sum(data['PRICE'] * data['SIZE'])})
```


