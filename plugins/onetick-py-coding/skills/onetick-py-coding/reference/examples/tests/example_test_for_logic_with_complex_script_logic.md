# Example test for logic with complex script logic

Could you write a test for this function

```python
def func(source: otp.Source) -> otp.Source:
    def set_times_script(ticks):
        current_date = ticks['END_DATE']
        day_of_week = ticks['DAY_OF_WEEK']

        workdays = 0
        while workdays <= lookback_days + look_forward_days:
            if day_of_week in range(1, 6) or weekdays_only == 0:

                if workdays == look_forward_days:
                    ticks['POSITION_CHECK_END_DATE'] = current_date

                if workdays == num_days_position_accum + look_forward_days:
                    ticks['POSITION_CHECK_START_DATE'] = current_date
                    ticks['POSITION_STATS_END_DATE'] = current_date - otp.Day(1)

                if workdays == num_days_position_stats + num_days_position_accum + look_forward_days:
                    ticks['POSITION_STATS_START_DATE'] = current_date

                if price_move_check:
                    if workdays == num_days_md + look_forward_days:
                        ticks['PRICE_STATS_START_DATE'] = current_date

                workdays += 1

            day_of_week -= 1
            if day_of_week == 0:
                day_of_week = 7
            current_date -= otp.Day(1)

    source.script(set_times_script, inplace=True)
    return source
```

## Answer

Here is an example of the test
```python
import pytest
import onetick.py as otp

# Assuming the function is defined in a module named my_module
def func(source: otp.Source) -> otp.Source:
    def set_times_script(ticks):
        current_date = ticks['END_DATE']
        day_of_week = ticks['DAY_OF_WEEK']

        workdays = 0
        while workdays <= lookback_days + look_forward_days:
            if day_of_week in range(1, 6) or weekdays_only == 0:

                if workdays == look_forward_days:
                    ticks['POSITION_CHECK_END_DATE'] = current_date

                if workdays == num_days_position_accum + look_forward_days:
                    ticks['POSITION_CHECK_START_DATE'] = current_date
                    ticks['POSITION_STATS_END_DATE'] = current_date - otp.Day(1)

                if workdays == num_days_position_stats + num_days_position_accum + look_forward_days:
                    ticks['POSITION_STATS_START_DATE'] = current_date

                if price_move_check:
                    if workdays == num_days_md + look_forward_days:
                        ticks['PRICE_STATS_START_DATE'] = current_date

                workdays += 1

            day_of_week -= 1
            if day_of_week == 0:
                day_of_week = 7
            current_date -= otp.Day(1)

    source.script(set_times_script, inplace=True)
    return source


@pytest.fixture
def default_session():
    with otp.TestSession() as s:
        yield s

def test_func(default_session):
    # Create a sample data source
    ticks = otp.Ticks([
        ['END_DATE', 'DAY_OF_WEEK'],
        [otp.dt(2023, 10, 6), 5],  # Friday
        [otp.dt(2023, 10, 5), 4],  # Thursday
        [otp.dt(2023, 10, 4), 3],  # Wednesday
    ])

    # Set global variables used in the function
    global lookback_days, look_forward_days, weekdays_only
    global num_days_position_accum, num_days_position_stats, num_days_md
    global price_move_check

    lookback_days = 5
    look_forward_days = 2
    weekdays_only = 1
    num_days_position_accum = 3
    num_days_position_stats = 4
    num_days_md = 2
    price_move_check = True

    # Apply the function
    source = func(ticks)

    # Run the source to get the result
    result = otp.run(source)

    # Print the result for debugging
    print(result)

    # Add assertions to verify the expected behavior
    assert 'POSITION_CHECK_END_DATE' in result.columns
    assert 'POSITION_CHECK_START_DATE' in result.columns
    assert 'POSITION_STATS_END_DATE' in result.columns
    assert 'POSITION_STATS_START_DATE' in result.columns
    if price_move_check:
        assert 'PRICE_STATS_START_DATE' in result.columns

    # Add more specific assertions based on expected values
    # Example:
    assert result['POSITION_CHECK_END_DATE'][0] == otp.dt(2023, 10, 6) - otp.Day(2)
    assert result['POSITION_CHECK_START_DATE'][0] == otp.dt(2023, 10, 6) - otp.Day(5)
    assert result['POSITION_STATS_END_DATE'][0] == otp.dt(2023, 10, 6) - otp.Day(6)
    assert result['POSITION_STATS_START_DATE'][0] == otp.dt(2023, 10, 6) - otp.Day(10)
    if price_move_check:
        assert result['PRICE_STATS_START_DATE'][0] == otp.dt(2023, 10, 6) - otp.Day(4)

```
