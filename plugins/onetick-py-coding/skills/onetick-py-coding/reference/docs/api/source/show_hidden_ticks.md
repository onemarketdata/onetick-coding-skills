# otp.Source.show_hidden_ticks

#### ``Source.show_hidden_ticks(inplace=False)``

Propagates all of a tick’s fields without changing their values. Propagates all ticks,
even those with a status not equal to 0, which are normally hidden.

Use this method to display all original ticks and correction ticks in the same time series in the same
time sequence in which they were originally sent with a filter to remove unneeded **TICK_STATUS** values.

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Show hidden ticks and filter them by choosing non-zero *TICK_STATUS* values:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.show_hidden_ticks()
>>> data = data.where(data['TICK_STATUS'] != 0)
>>> data = data[['PRICE', 'SIZE', 'TICK_STATUS']]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                            Time     PRICE  SIZE  TICK_STATUS
0  2024-02-01 11:50:15.621123654  185.6950     1            1
1  2024-02-01 14:43:17.690903111  185.6950     1            4
2  2024-02-01 14:43:17.690903111  185.6950     1            7
3  2024-02-01 15:36:14.597279750  186.6100    50            1
4  2024-02-01 15:42:02.658055953  186.8700     1            1
5  2024-02-01 15:42:02.661911428  186.8700     1            1
6  2024-02-01 15:46:38.890972031  186.6100    50            4
7  2024-02-01 15:46:38.890972031  186.6100    50            7
8  2024-02-01 15:46:38.891369862  186.6100    45            1
9  2024-02-01 15:49:02.043717846  186.6100    45            4
10 2024-02-01 15:49:02.043717846  186.6100    45            7
11 2024-02-01 15:57:29.621873687  186.7078   306            1
12 2024-02-01 16:06:23.115605520  186.7078   306            4
13 2024-02-01 16:06:23.115605520  186.7078   306            7
14 2024-02-01 16:15:02.784032763  186.8700     1            4
15 2024-02-01 16:15:02.784032763  186.8700     1            7
16 2024-02-01 16:15:02.801559379  186.8700     1            4
17 2024-02-01 16:15:02.801559379  186.8700     1            7
```

##### SEE ALSO
**SHOW_HIDDEN_TICKS** OneTick event processor
``show_corrected_ticks()``
``correct_tick_filter()``
