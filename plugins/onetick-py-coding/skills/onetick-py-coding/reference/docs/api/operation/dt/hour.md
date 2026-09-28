# otp.Operation.dt.hour

### ``hour(timezone)``

Return the hour.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, 1, i, 0, 6) for i in range(10, 17)])
>>> data['HOUR'] = data['X'].dt.hour()
>>> otp.run(data)[['X', 'HOUR']]
                    X  HOUR
0 2022-05-01 10:00:06    10
1 2022-05-01 11:00:06    11
2 2022-05-01 12:00:06    12
3 2022-05-01 13:00:06    13
4 2022-05-01 14:00:06    14
5 2022-05-01 15:00:06    15
6 2022-05-01 16:00:06    16
```
