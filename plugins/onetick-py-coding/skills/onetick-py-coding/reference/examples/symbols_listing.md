# get all symbols from some database

Code shows how to get all symbols for the US_COMP database on 2023/12/6

```python
import onetick.py as otp

symbols = otp.Symbols(db='US_COMP')

df = otp.run(symbols, date=otp.dt(2023, 12, 6))
```

# Get available symbols by pattern

Code shows how to get all tickers (symbols) start with the A character from US_COMP database on 2023/11/9

```python
import onetick.py as otp

symbols = otp.Symbols(db='US_COMP', pattern='A%')

df = otp.run(symbols, date=otp.dt(2023, 11, 9)
```
