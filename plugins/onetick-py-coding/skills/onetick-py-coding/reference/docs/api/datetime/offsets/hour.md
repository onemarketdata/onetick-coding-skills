# otp.Hour

### ``Hour(n)``

Object representing hour’s datetime offset.

Can be added to or subtracted from:

* ``otp.datetime`` objects
* ``Source`` columns of datetime type

* **Parameters:**
  **n** (int, ``Column``, ``Operation``) – Offset integer value or column of ``Source``.
  The only ``Operation`` supported is
  subtracting one datetime column from another. See example below.

##### Examples

Add to or subtract from ``otp.datetime`` object:

```
>>> otp.datetime(2012, 12, 12, 12) + otp.Hour(1)
2012-12-12 13:00:00
>>> otp.datetime(2012, 12, 12, 12) - otp.Hour(1)
2012-12-12 11:00:00
```

Use offset in columns:

```
>>> t = otp.Tick(A=1)
>>> t['T'] = otp.datetime(2012, 12, 12, 12)
>>> t['T'] += otp.Hour(t['A'])
>>> otp.run(t)
        Time                   T  A
0 2003-12-01 2012-12-12 13:00:00  1
```

Use it to calculate difference between two dates:

```
>>> t = otp.Tick(A=otp.dt(2022, 1, 1), B=otp.dt(2022, 1, 2))
>>> t['DIFF'] = otp.Hour(t['B'] - t['A'])
>>> otp.run(t)
        Time           A           B  DIFF
0 2003-12-01  2022-01-01  2022-01-02    24
```
