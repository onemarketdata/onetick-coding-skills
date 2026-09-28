# otp.Operation.dt.day_name

### ``day_name(timezone)``

Returns the name of the weekday.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, 5, i) for i in range(10, 17)])
>>> data['DAY_NAME'] = data['X'].dt.day_name()
>>> otp.run(data)[['X', 'DAY_NAME']]
           X   DAY_NAME
0 2022-05-10    Tuesday
1 2022-05-11  Wednesday
2 2022-05-12   Thursday
3 2022-05-13     Friday
4 2022-05-14   Saturday
5 2022-05-15     Sunday
6 2022-05-16     Monday
```
