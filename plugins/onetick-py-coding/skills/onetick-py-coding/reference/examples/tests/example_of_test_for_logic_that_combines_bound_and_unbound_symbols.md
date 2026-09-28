# Example of test for logic that combines bound and unbound symbols

Here is an example of how to test logic with mixture of bound and unbound sources

```python
import pytest
import onetick.py as otp


@pytest.fixture(scope='module')
def session():
    with otp.TestSession() as s:
        yield s


def create_ticks():
    trades_data = {
        'PRICE': [100.0, 101.0, 102.0, 103.0, 104.0],
        'SIZE': [10, 20, 30, 40, 50],
        'offset': [0, 60, 120, 180, 240]
    }
    return otp.Ticks(trades_data)


def create_db(session, trades):
    db = otp.DB('SOME_DB')
    # put data into the 'date' that's consistent with
    # the query interval ('start' and 'end' params in the 'otp.run')
    db.add(trades, symbol='AAI', tick_type='TRD', date=otp.dt(2003, 12, 1))
    db.add(trades, symbol='A', tick_type='TRD', date=otp.dt(2003, 12, 1))
    db.add(trades, symbol='ABAX', tick_type='TRD', date=otp.dt(2003, 12, 1))
    session.use(db)
    return db


def logic_with_bound_and_unbound_symbols():
    # AAI symbol is bound here
    aai_trades = otp.DataSource(db='SOME_DB', tick_type='TRD', symbol='AAI', schema_policy='manual')
    aai_trades.schema.set(PRICE=float, SIZE=int)
    aai_trades = aai_trades[['PRICE', 'SIZE']]
    aai_trades = aai_trades.agg({'AAI_VWAP': otp.agg.vwap('PRICE', 'SIZE')}, bucket_interval=60)

    # No symbols specified here, will use unbound symbols later
    other_trades = otp.DataSource(db='DEMO_L1', tick_type='TRD', schema_policy='manual')
    other_trades.schema.set(PRICE=float, SIZE=int)
    other_trades = other_trades[['PRICE', 'SIZE']]
    other_trades = other_trades.agg({'OTHER_VWAP': otp.agg.vwap('PRICE', 'SIZE')}, bucket_interval=60)

    # Join the two data sources
    data = otp.join(aai_trades, other_trades, on='same_size')

    # Calculate the correlation between AAI_VWAP and OTHER_VWAP
    data = data.agg({'CORRELATION': otp.agg.correlation('AAI_VWAP', 'OTHER_VWAP')})

    # Run the query with unbound symbols
    return otp.run(
        data,
        symbols=['DEMO_L1::A', 'DEMO_L1::ABAX'],
        date=otp.dt(2003, 12, 1)
    )


def test_vwap_correlation(session):
    trades = create_ticks()
    db = create_db(session, trades)

    dfs = logic_with_bound_and_unbound_symbols()

    # Assert that the resulting DataFrame has the expected length and fields
    for key, df in dfs.items():
        assert len(df) > 0
        assert 'CORRELATION' in df.columns
        print(f'Correlation AAI vs {key}: ')
        print(df)
```
