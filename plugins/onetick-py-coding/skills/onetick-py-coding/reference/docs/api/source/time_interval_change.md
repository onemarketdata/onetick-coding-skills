# otp.Source.time_interval_change

#### ``Source.time_interval_change(start_change=0, end_change=0, inplace=False)``

Changing time interval by making it bigger or smaller.

All timestamps of ticks that are crossing the border of original time range
will be set to original start time or end time depending on their original time.

* **Parameters:**
  * **start_change** (int or `datetime offset`) – Offset to shift start time.
    Can be positive or negative.
    Positive value moves start time into the future, negative – to the past.
    int values are interpreted as milliseconds.
  * **end_change** (int or `datetime offset`) – Offset to shift end time.
    Can be positive or negative.
    Positive value moves end time into the future, negative – to the past.
    int values are interpreted as milliseconds.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

```
>>> start = otp.dt(2024, 2, 1, 4) + otp.Milli(9)
>>> end = otp.dt(2024, 2, 1, 4) + otp.Milli(12)
>>> data = otp.DataSource('US_COMP_SAMPLE', symbols='AAPL', tick_type='TRD')
>>> data = data[['PRICE', 'SIZE']]
```

By default, `time_interval_change()` does nothing:

```
>>> t = data.time_interval_change()
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.010381671  185.49     1
1 2024-02-01 04:00:00.011224206  185.50     2
2 2024-02-01 04:00:00.011671193  185.50     1
```

Decreasing time range will not change ticks’ timestamps:

```
>>> t = data.time_interval_change(start_change=otp.Milli(1), end_change=-otp.Milli(1))
>>> otp.run(t, start=start, end=end)
                           Time   PRICE  SIZE
0 2024-02-01 04:00:00.010381671  185.49     1
```

Increasing time range will change timestamps of the ticks that crossed the border.
In this case first 3 ticks timestamps will be set to original start time,
and last 6 ticks – to original end time.

```
>>> t = data.time_interval_change(start_change=-otp.Milli(1), end_change=otp.Milli(1))
>>> otp.run(t, start=start, end=end)
                            Time   PRICE  SIZE
0  2024-02-01 04:00:00.009000000  186.50     6
1  2024-02-01 04:00:00.009000000  185.59     1
2  2024-02-01 04:00:00.009000000  185.49   107
3  2024-02-01 04:00:00.010381671  185.49     1
4  2024-02-01 04:00:00.011224206  185.50     2
5  2024-02-01 04:00:00.011671193  185.50     1
6  2024-02-01 04:00:00.012000000  185.50     2
7  2024-02-01 04:00:00.012000000  185.50    45
8  2024-02-01 04:00:00.012000000  185.68    11
9  2024-02-01 04:00:00.012000000  185.68    10
10 2024-02-01 04:00:00.012000000  185.68    23
11 2024-02-01 04:00:00.012000000  185.72     5
```

##### SEE ALSO
``onetick.py.Source.modify_query_times()``

``onetick.py.Source.time_interval_shift()``

