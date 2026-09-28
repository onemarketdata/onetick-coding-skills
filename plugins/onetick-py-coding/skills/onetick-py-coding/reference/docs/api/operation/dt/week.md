# otp.Operation.dt.week

### ``week(timezone)``

Returns the week.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2020, i, 1) for i in range(3, 11)])
>>> data['WEEK'] = data['X'].dt.week()
>>> otp.run(data)[['X', 'WEEK']]
           X  WEEK
0 2020-03-01    10
1 2020-04-01    14
2 2020-05-01    18
3 2020-06-01    23
4 2020-07-01    27
5 2020-08-01    31
6 2020-09-01    36
7 2020-10-01    40
```
