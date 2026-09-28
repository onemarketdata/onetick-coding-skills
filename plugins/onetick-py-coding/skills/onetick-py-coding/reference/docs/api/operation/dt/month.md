# otp.Operation.dt.month

### ``month(timezone)``

Return the month.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, i, 1) for i in range(3, 11)])
>>> data['MONTH'] = data['X'].dt.month()
>>> otp.run(data)[['X', 'MONTH']]
           X  MONTH
0 2022-03-01      3
1 2022-04-01      4
2 2022-05-01      5
3 2022-06-01      6
4 2022-07-01      7
5 2022-08-01      8
6 2022-09-01      9
7 2022-10-01     10
```
