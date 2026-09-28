# otp.Quarter

### ``Quarter(n)``

Object representing quarter’s datetime offset.

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
>>> otp.datetime(2012, 12, 12, 12) + otp.Quarter(1)
2013-03-12 12:00:00
>>> otp.datetime(2012, 12, 12, 12) - otp.Quarter(1)
2012-09-12 12:00:00
```

Use offset in columns:

```
>>> t = otp.Tick(A=1)
>>> t['T'] = otp.datetime(2012, 12, 12, 12, tz='GMT')
>>> t['T'] += otp.Quarter(t['A'])
>>> otp.run(t, start=otp.datetime(2003, 12, 2), end=otp.datetime(2003, 12, 3), timezone='GMT')
        Time                   T  A
0 2003-12-02 2013-03-12 12:00:00  1
```

Use it to calculate difference between two dates:

```
>>> t = otp.Tick(A=otp.dt(2022, 1, 1), B=otp.dt(2023, 1, 1))
>>> t['DIFF'] = otp.Quarter(t['B'] - t['A'])
>>> otp.run(t)
        Time           A           B  DIFF
0 2003-12-01  2022-01-01  2023-01-01     4
```
