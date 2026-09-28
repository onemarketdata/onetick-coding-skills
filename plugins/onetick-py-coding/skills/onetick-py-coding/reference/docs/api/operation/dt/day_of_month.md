# otp.Operation.dt.day_of_month

### ``day_of_month(timezone)``

Return the day of the month.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, i) for i in range(10, 17)])
>>> data['DAY_OF_MONTH'] = data['X'].dt.day_of_month()
>>> otp.run(data)[['X', 'DAY_OF_MONTH']]
           X  DAY_OF_MONTH
0 2022-05-10            10
1 2022-05-11            11
2 2022-05-12            12
3 2022-05-13            13
4 2022-05-14            14
5 2022-05-15            15
6 2022-05-16            16
```
