# otp.Operation.dt.minute

### ``minute(timezone)``

Return the minute.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, 1, 15, i, 6) for i in range(10, 17)])
>>> data['MINUTE'] = data['X'].dt.minute()
>>> otp.run(data)[['X', 'MINUTE']]
                    X  MINUTE
0 2022-05-01 15:10:06      10
1 2022-05-01 15:11:06      11
2 2022-05-01 15:12:06      12
3 2022-05-01 15:13:06      13
4 2022-05-01 15:14:06      14
5 2022-05-01 15:15:06      15
6 2022-05-01 15:16:06      16
```
