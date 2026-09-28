# otp.Milli

### ``Milli(n)``

Object representing millisecond’s datetime offset.

Can be added to or subtracted from:

* ``otp.datetime`` objects
* ``Source`` columns of datetime type

* **Parameters:**
  **n** (int, ``Column``, ``Operation``, float) – Offset integer value or column of ``Source``.
  The only ``Operation`` supported is
  subtracting one datetime column from another. See example below.
  Offset could be `float` to pass a fractional time unit value.

##### Examples

Add to or subtract from ``otp.datetime`` object:

```
>>> otp.datetime(2012, 12, 12, 12) + otp.Milli(1)
2012-12-12 12:00:00.001000
>>> otp.datetime(2012, 12, 12, 12) - otp.Milli(1)
2012-12-12 11:59:59.999000
```

Using `float` value to pass nanoseconds:

```
>>> otp.datetime(2012, 12, 12, 12) + otp.Milli(1.000123)
2012-12-12 12:00:00.001000123
```

Use offset in columns:

```
>>> t = otp.Tick(A=1)
>>> t['T'] = otp.datetime(2012, 12, 12, 12)
>>> t['T'] += otp.Milli(t['A'])
>>> otp.run(t)
        Time                       T  A
0 2003-12-01 2012-12-12 12:00:00.001  1
```

Use it to calculate difference between two dates:

```
>>> t = otp.Tick(A=otp.dt(2022, 1, 1), B=otp.dt(2022, 1, 1, 0, 0, 1))
>>> t['DIFF'] = otp.Milli(t['B'] - t['A'])
>>> otp.run(t)
        Time           A                    B  DIFF
0 2003-12-01  2022-01-01  2022-01-01 00:00:01  1000
```
