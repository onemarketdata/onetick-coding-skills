# otp.Operation.dt.day_of_year

### ``day_of_year(timezone)``

Return the day of the year.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, i) for i in range(10, 17)])
>>> data['DAY_OF_YEAR'] = data['X'].dt.day_of_year()
>>> otp.run(data)[['X', 'DAY_OF_YEAR']]
           X  DAY_OF_YEAR
0 2022-05-10          130
1 2022-05-11          131
2 2022-05-12          132
3 2022-05-13          133
4 2022-05-14          134
5 2022-05-15          135
6 2022-05-16          136
```
