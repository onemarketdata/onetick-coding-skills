# otp.Ticks purpose

The otp.Ticks is very handy in testing because it allows to emulate ticks sequence to check code.
Also the otp.Ticks might be helpful in the following cases:
* integrate OneTick ticks with other sources like pandas DataFrame, CSV or other
* incorporate static info into the logic with ticks

# Create ticks at a specific time

By default ticks timestamps created by the otp.Ticks starts from the query interval and stand 1 millisecond from each other.
There are two ways to adjust timestamps for generated ticks.

1. Use the `offset` reserved field. It sets offsets from the start query interval. 
It can be expressed in different units like `otp.Nano`, `otp.Seconds`, `otp.Minute`, etc

Example
```python
data = otp.Ticks(
    X=[1, 2, 3], 
    offset=[0, otp.Nano(1), otp.Second(5)]
)
otp.run(data)
```

2. Use the pandas Dataframe and the `Time` field.

Example
```python
start_datetime = datetime(2023, 1, 1, 12)
time_array = [start_datetime + otp.Hour(1) + otp.Nano(1)]
a_array = [start_datetime - otp.Day(15) - otp.Nano(7)]
df = pd.DataFrame({'Time': time_array,'A': a_array})
data = otp.Ticks(df)
otp.run(data, start=start_datetime, end=start_datetime + otp.Day(1))
```

*Limitation* You need to always check that your timestamp belongs to the query interval
