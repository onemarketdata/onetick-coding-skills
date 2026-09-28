# otp.Source.show_corrected_ticks

#### ``Source.show_corrected_ticks(inplace=False)``

Shows ticks which were corrected or canceled within the interval of the query.
Only corrected and correction ticks will be propagated.

A *TICK_STATUS* field is added to each tick.

For Corrections:

* Old tick TICK_STATUS = 5
* New tick TICK_STATUS = 6

For Cancellations:

* Old tick TICK_STATUS = 4
* New tick TICK_STATUS = 7

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Show corrected or canceled ticks:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.show_corrected_ticks()
>>> data = data[['PRICE', 'SIZE', 'TICK_STATUS']]
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                            Time     PRICE  SIZE  TICK_STATUS
0  2024-02-01 14:43:17.690903111  185.6950     1            4
1  2024-02-01 14:43:17.690903111  185.6950     1            7
2  2024-02-01 15:46:38.890972031  186.6100    50            4
3  2024-02-01 15:46:38.890972031  186.6100    50            7
4  2024-02-01 15:49:02.043717846  186.6100    45            4
5  2024-02-01 15:49:02.043717846  186.6100    45            7
6  2024-02-01 16:06:23.115605520  186.7078   306            4
7  2024-02-01 16:06:23.115605520  186.7078   306            7
8  2024-02-01 16:15:02.784032763  186.8700     1            4
9  2024-02-01 16:15:02.784032763  186.8700     1            7
10 2024-02-01 16:15:02.801559379  186.8700     1            4
11 2024-02-01 16:15:02.801559379  186.8700     1            7
```

##### SEE ALSO
**SHOW_CORRECTED_TICKS** OneTick event processor
``show_hidden_ticks()``
``correct_tick_filter()``
