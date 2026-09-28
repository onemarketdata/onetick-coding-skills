# Example of test for logic in join_with_query

Here is an example of how to test queries with `join_with_query` functions.
Also this example illustrates how to test and emulate symbol parameters passed
into a sub-query (`db` parameter of the `market_vol` sub-query, and query interval
parameters like `start` and `end`)

```python
import pytest
import onetick.py as otp
import pandas as pd


@pytest.fixture(scope="function")
def session_():
    """
    Fixture to create a TestSession for the duration of the test.
    """
    session = otp.TestSession()
    yield session


def create_ticks():
    """
    Create sample tick data for orders and trades.

    Returns:
        tuple: A tuple containing orders, AAPL trades, and MSFT trades.
    """
    start = otp.dt(2023, 5, 15, 9, 30)
    end = otp.dt(2023, 5, 15, 9, 30, 1)

    # Create order ticks
    orders = otp.Ticks(
        TIME_MIN=[start, start + otp.Milli(500)],
        TIME_MAX=[end, end + otp.Milli(500)],
        MD_SYMBOL=['AAPL', 'MSFT'],
        MD_DB=['US_COMP', 'NASDAQ'],
        SIZE=[100, 200]
    )

    # Create trade ticks for AAPL on US_COMP
    aapl_us_comp_trades = otp.Ticks(
        offset=[otp.Hour(9) + otp.Minute(30)],
        SIZE=[50],
    )

    # Create trade ticks for MSFT on NASDAQ
    msft_nasdaq_trades = otp.Ticks(
        offset=[otp.Hour(9) + otp.Minute(30) + otp.Second(1)],
        SIZE=[150],
    )

    return orders, aapl_us_comp_trades, msft_nasdaq_trades


def market_vol(symbol, db):
    """
    Calculate market volume for a given symbol and database.

    Args:
        symbol (str): The symbol for which to calculate market volume.
        db (str): The database from which to retrieve trade data.

    Returns:
        otp.DataSource: A DataSource object with aggregated market volume.
    """
    trd = otp.DataSource(
        db=db,
        tick_type='TRD',
    )
    trd.schema['SIZE'] = int  # Ensure the SIZE field is of type int

    # Aggregate the market volume
    trd = trd.agg(
        {
            'VOLUME_MARKET': otp.agg.sum('SIZE'),
        },
    )
    return trd


def main_query():
    # Define the DataSource for orders
    orders = otp.DataSource(db='ORDER_DB', tick_type='ORDER')

    # Join orders with the market volume query
    orders = orders.join_with_query(market_vol,
                                    start=orders['TIME_MIN'],
                                    end=orders['TIME_MAX'],
                                    symbol=orders['MD_SYMBOL'],
                                    params=dict(db=orders['MD_DB']))

    return orders


def test_market_vol(session_):
    """
    Test the market volume calculation by joining order data with trade data.

    Args:
        session_ (otp.TestSession): The test session fixture.
    """
    # Create sample ticks
    orders, aapl_us_comp_trades, msft_nasdaq_trades = create_ticks()

    # Create and add order data to the ORDER_DB
    db = otp.DB("ORDER_DB")
    db.add(orders, tick_type='ORDER')

    # Create and add AAPL trade data to the US_COMP database
    us_comp_db = otp.DB("US_COMP")
    # put data into the 'date' that's consistent with
    # the query interval ('start' and 'end' params in the 'otp.run')
    us_comp_db.add(aapl_us_comp_trades, symbol='AAPL', tick_type='TRD', date=otp.dt(2023, 5, 15))

    # Create and add MSFT trade data to the NASDAQ database
    nasdaq_db = otp.DB("NASDAQ")
    nasdaq_db.add(msft_nasdaq_trades, symbol='MSFT', tick_type='TRD', date=otp.dt(2023, 5, 15))

    # Use the databases in the session
    session_.use(db)
    session_.use(us_comp_db)
    session_.use(nasdaq_db)

    # Run the query and convert the result to a pandas DataFrame
    result = otp.run(main_query())
    df = pd.DataFrame(result)

    # Assert the length of the resulting DataFrame
    assert len(df) == 2
    # Assert the presence of the VOLUME_MARKET column
    assert 'VOLUME_MARKET' in df.columns
    # Assert the values of the VOLUME_MARKET column
    assert df['VOLUME_MARKET'].iloc[0] == 50
    assert df['VOLUME_MARKET'].iloc[1] == 150
```
