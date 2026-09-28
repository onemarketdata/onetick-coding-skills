# otp.Operation.dt.year

### ``year(timezone)``

Return the year.

* **Parameters:**
  **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
  By default, the timezone of the query will be used.

##### Examples

```
>>> data = otp.Ticks(X=[otp.dt(2020 + i, 3, 1) for i in range(3, 11)])
>>> data['YEAR'] = data['X'].dt.year()
>>> otp.run(data)[['X', 'YEAR']]
           X  YEAR
0 2023-03-01  2023
1 2024-03-01  2024
2 2025-03-01  2025
3 2026-03-01  2026
4 2027-03-01  2027
5 2028-03-01  2028
6 2029-03-01  2029
7 2030-03-01  2030
```
