# Example of test for logic with bound symbols in merge

Here is an example of how to test logic with bound symbols
(merge mutlitple symbols)

```python
import pytest
import onetick.py as otp
from datetime import datetime


@pytest.fixture
def session():
    return otp.TestSession()


@pytest.fixture
def create_ticks(session):
    # Create necessary ticks
    ticks_data = {
        'SIZE': [100, 200, 300, 400, 500],
        'PRICE': [10, 20, 30, 40, 50],
        'offset': [otp.Nano(1), otp.Nano(2), otp.Nano(3), otp.Nano(4), otp.Nano(5)]
    }
    ticks = otp.Ticks(ticks_data)
    db = otp.DB('US_COMP')
    # put data into the 'date' that's consistent with
    # the query interval ('start' and 'end' params in the 'otp.run')
    db.add(ticks, symbol='AAPL', tick_type='TRD', date=otp.dt(2023, 12, 6))
    db.add(ticks, symbol='AMZN', tick_type='TRD', date=otp.dt(2023, 12, 6))
    db.add(ticks, symbol='MSFT', tick_type='TRD', date=otp.dt(2023, 12, 6))
    db.add(ticks, symbol='GOOGL', tick_type='TRD', date=otp.dt(2023, 12, 6))
    db.add(ticks, symbol='FB', tick_type='TRD', date=otp.dt(2023, 12, 6))
    session.use(db)


def bound_symbols_logic():
    # Merge results for all symbols together
    cross_symbol = otp.merge(trades, symbols=otp.Symbols(db='US_COMP'))

    # Sort and select last 5 with largest volume
    cross_symbol = cross_symbol.sort('VOLUME')
    top_symbols = cross_symbol[-5:]

    # Run the query
    return otp.run(top_symbols, date=otp.dt(2023, 12, 6))


def test_top_traded_symbols(session, create_ticks):
    # Calculate volume per symbol
    trades = otp.DataSource(db='US_COMP', tick_type='TRD')
    trades = trades.agg({'VOLUME': otp.agg.sum('SIZE')})
    trades['TICKER'] = trades.Symbol.name

    df = bound_symbols_logic()

    # Assertions
    assert len(df) == 5
    assert 'VOLUME' in df.columns
    assert 'TICKER' in df.columns
    assert df['VOLUME'].is_monotonic_increasing
```
