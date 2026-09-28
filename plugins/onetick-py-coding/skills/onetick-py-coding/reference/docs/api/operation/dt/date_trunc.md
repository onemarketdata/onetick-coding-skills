# otp.Operation.dt.date_trunc

### ``date_trunc(date_part, timezone)``

Truncates to the specified precision.

* **Parameters:**
  * **date_part** (*str* *|* *Operation* *|* *Column*) – Precision to truncate datetime to. Possible values are ‘year’, ‘quarter’, ‘month’, ‘week’, ‘day’, ‘hour’,
    ‘minute’, ‘second’, ‘millisecond’ and ‘nanosecond’.
    Notice that beginning of week is considered to be Sunday.
  * **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
    By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2020, 11, 11, 5, 4, 13, 101737, 879)] * 7,
...                  DATE_PART=['year', 'day', 'hour', 'minute', 'second', 'millisecond', 'nanosecond'])
>>> data['TRUNCATED_X'] = data['X'].dt.date_trunc(data['DATE_PART'])
>>> otp.run(data)[['X', 'TRUNCATED_X', 'DATE_PART']]
                              X                   TRUNCATED_X    DATE_PART
0 2020-11-11 05:04:13.101737879 2020-01-01 00:00:00.000000000         year
1 2020-11-11 05:04:13.101737879 2020-11-11 00:00:00.000000000          day
2 2020-11-11 05:04:13.101737879 2020-11-11 05:00:00.000000000         hour
3 2020-11-11 05:04:13.101737879 2020-11-11 05:04:00.000000000       minute
4 2020-11-11 05:04:13.101737879 2020-11-11 05:04:13.000000000       second
5 2020-11-11 05:04:13.101737879 2020-11-11 05:04:13.101000000  millisecond
6 2020-11-11 05:04:13.101737879 2020-11-11 05:04:13.101737879   nanosecond
```
