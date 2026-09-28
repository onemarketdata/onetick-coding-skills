# Retrieve a data tick at a specified exact time

It shows how to understand state at specified exact time / get a tick at some time.

```python
def get_data(datetime_point: otp.dt):
    data = otp.DataSource(
        db=...,
        symbol=...,
        back_to_first_tick=otp.Hour(1)  # we need to get the nearest prior tick in case if there is no tick in exact time 
    )

    # get only the last tick in case if there are several ticks with exactly the same timestamp
    data = otp.agg.last_tick().apply(data)

    df = otp.run(
        data,
        start=datetime_point, 
        end=datetime_point  # `start` and `end` set to the same values to speed up a lookup
    )

```

Usually it refers to point-in-time (PIT) request.
Possible usecases: 
- find a ticker price at some time
- find spread at some time
- get snapshot of orders at some time