# otp.Operation.dt.month_name

### ``month_name(timezone)``

Return name of the month.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2022, i, 1) for i in range(3, 11)])
>>> data['MONTH_NAME'] = data['X'].dt.month_name()
>>> otp.run(data)[['X', 'MONTH_NAME']]
           X MONTH_NAME
0 2022-03-01        Mar
1 2022-04-01        Apr
2 2022-05-01        May
3 2022-06-01        Jun
4 2022-07-01        Jul
5 2022-08-01        Aug
6 2022-09-01        Sep
7 2022-10-01        Oct
```
