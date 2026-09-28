# otp.Operation.dt.second

### ``second(timezone)``

Return the second.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, 1, 15, 11, i) for i in range(10, 17)])
>>> data['SECOND'] = data['X'].dt.second()
>>> otp.run(data)[['X', 'SECOND']]
                    X  SECOND
0 2022-05-01 15:11:10      10
1 2022-05-01 15:11:11      11
2 2022-05-01 15:11:12      12
3 2022-05-01 15:11:13      13
4 2022-05-01 15:11:14      14
5 2022-05-01 15:11:15      15
6 2022-05-01 15:11:16      16
```
