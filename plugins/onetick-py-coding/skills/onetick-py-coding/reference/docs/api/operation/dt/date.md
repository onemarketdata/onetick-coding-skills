# otp.Operation.dt.date

### ``date()``

Return a new ``onetick.py.nsectime`` type operation filled with date only.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2019, 1, 1, 1, 1, 1), otp.dt(2019, 2, 2, 2, 2, 2)])
>>> data["X"] = data["X"].dt.date()
>>> df = otp.run(data, timezone="GMT")
>>> df["X"]
0   2019-01-01
1   2019-02-02
Name: X, dtype: datetime64[ns]
```
