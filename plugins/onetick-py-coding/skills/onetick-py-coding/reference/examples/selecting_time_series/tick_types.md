# what is tick type and how to set it up

Every source should define tick type. If there is no tick type then OneTick raises an error `No tick type was specified for at least one branch of the execution graph`

```python
import onetick.py as otp

trades = otp.DataSource(db='DEMO_L1', tick_type='TRD')

# tick type is not specified here
quotes = otp.DataSource(db='DEMO_L1')

data = otp.join_by_time(
    [
        trades[['PRICE']],
        quotes[['ASK_PRICE', 'BID_PRICE']]
    ]
)

df = otp.run(data,
             symbols='AAPL',
             date=otp.dt(2003, 12, 1))
```

OneTick allows to put non existing tick types in the database, because it has some sources that don’t use tick types to get / generate data.
Using unexsiting tick types on sources that uses tick type to get data will just return no ticks.
