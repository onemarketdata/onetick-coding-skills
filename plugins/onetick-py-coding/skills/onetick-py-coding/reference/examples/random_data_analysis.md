# The largest number of consecutive upticks

To find the largest bucket with consecutive upticks (where each tick's price is not less than the previous one) for trades from the US_COMP database for the AAPL symbol on 2023/12/23, you'll need to aggregate the data with a custom bucketing condition. Here's a full code example using onetick.py to achieve this:

```python
import onetick.py as otp

# Define the symbol and database
symbol = 'AAPL'
database = 'US_COMP'
date = otp.dt(2023, 12, 23)

# Load trades data from US_COMP database
trades = otp.DataSource(database, tick_type='TRD', symbol=symbol)

# Aggregation logic to find consecutive upticks
# Bucket end condition is set where current price is less than the previous price
consecutive_upticks = trades.agg({'COUNT': otp.agg.count(),
                                  'FIRST_TIME': otp.agg.first('Time'),
                                  'LAST_TIME': otp.agg.last('Time')},
                                 bucket_units='flexible',
                                 bucket_end_condition=trades['PRICE'] < trades['PRICE'][-1])

# Finding the largest bucket
# Sorting by COUNT in descending order and taking the first entry
largest_uptick_bucket = consecutive_upticks.sort('COUNT', ascending=False).first()

# Run the query for the specified date
df = otp.run(largest_uptick_bucket, date=date)
```

# Window with the largest volume
Write a function that finds the time period within a trading day where the highest volume of trades occurred, aggregated in 10-minute intervals.

Here is code example:

```python
def largest_traded_volume(trades_db : str, symbol : str) -> otp.DataSource:
    # Load trades data
    trades = otp.DataSource(trades_db, tick_type='TRD', symbol=symbol)

    # Aggregate the trades data to calculate the total volume in 10-minute buckets
    volume_agg = trades.agg(
        {'VOLUME': otp.agg.sum('SIZE')},
        bucket_interval=otp.Minute(10),
    )

    # Find the bucket with the largest traded volume using high_tick aggregation
    largest_volume_bucket = otp.agg.high_tick('VOLUME', n=1, bucket_time='end').apply(volume_agg)
    return largest_volume_bucket
```

and here is also a test example for this code:

```python
import pytest
import onetick.py as otp


@pytest.fixture
def test_session():
    with otp.TestSession() as session:
        yield session


def test_largest_traded_volume_bucket(test_session):
    # Define the symbol and the database
    symbol = 'TEST_SYMBOL'
    trades_db = 'TEST_TRADES_DB'
    start_date = otp.dt(2023, 12, 1, 9, 30)
    end_date = otp.dt(2023, 12, 1, 16, 0)

    # Generate trade ticks
    ticks_data = [
        ['offset', 'SIZE'],
        [0, 100],  # Trade at 9:30
        [600000, 200],  # Trade at 9:40
        [1200000, 300],  # Trade at 9:50
        [1800000, 400],  # Trade at 10:00
        [2400000, 500],  # Trade at 10:10
    ]
    ticks = otp.Ticks(ticks_data)

    # Create the database and add ticks
    db = otp.DB(trades_db)
    # put data into the 'date' that's consistent with
    # the query interval ('start' and 'end' params in the 'otp.run')
    db.add(ticks, symbol=symbol, date=start_date)
    test_session.use(db)

    # call the logic
    largest_volume_bucket = largest_traded_volume(trades_db, symbol)

    # Run the query for the specified date range
    df = otp.run(largest_volume_bucket, start=start_date, end=end_date)

    # Print the result
    print(df)

    # Assertions to validate the results
    assert not df.empty
    assert df['VOLUME'].iloc[0] == 500  # The largest volume should be 500
```
