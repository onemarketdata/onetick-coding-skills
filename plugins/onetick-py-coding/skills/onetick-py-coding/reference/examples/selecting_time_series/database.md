# how to set a database to fetch data

A database might be specified:

## For unbound case it is

```python
import onetick.py as otp
trades = otp.DataSource(tick_type='TRD')
df = otp.run(trades,
             symbols='DEMO_L1::AAPL',
             date=otp.dt(2003, 12, 1))
```

## and for bound symbols

```python
import onetick.py as otp
trades = otp.DataSource(symbol='DEMO_L1::AAPL', tick_type='TRD')
df = otp.run(trades,
             date=otp.dt(2003, 12, 1))
```

but setting up using the `db` parameter is *more preferable* (only for onetick.py, the OTQ doesn’t have db parameter) if it’s possible, ie

```python
import onetick.py as otp
trades = otp.DataSource(db='DEMO_L1', tick_type='TRD')
df = otp.run(trades,
             symbol='AAPL',
             date=otp.dt(2003, 12, 1))
```

## with a tick type

It doesn’t depent whether bound or unbound symbols are set

```python
import onetick.py as otp

trades = otp.DataSource(tick_type='DEMO_L1::TRD')

df = otp.run(trades,
             symbols='AAPL',
             date=otp.dt(2003, 12, 1))
```

## with a symbol and tick type together

With a symbol and tick type together in case if they don’t contradict each other

```python
import onetick.py as otp

trades = otp.DataSource(tick_type='DEMO_L1::TRD')

df = otp.run(trades,
             symbols='DEMO_L1::AAPL',
             date=otp.dt(2003, 12, 1))
```

or even

```python
import onetick.py as otp

trades = otp.DataSource(db='DEMO_L1', tick_type='DEMO_L1::TRD')

df = otp.run(trades,
             symbols='DEMO_L1::AAPL',
             date=otp.dt(2003, 12, 1))
```

OneTick requires that used *database should be defined in some of locators, local or remote, it doesn’t matter*.

