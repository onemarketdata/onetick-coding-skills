# otp.Operation.dt.quarter

### ``quarter(timezone)``

Return the quarter.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, i, 1) for i in range(3, 11)])
>>> data['QUARTER'] = data['X'].dt.quarter()
>>> otp.run(data)[['X', 'QUARTER']]
           X  QUARTER
0 2022-03-01        1
1 2022-04-01        2
2 2022-05-01        2
3 2022-06-01        2
4 2022-07-01        3
5 2022-08-01        3
6 2022-09-01        3
7 2022-10-01        4
```
