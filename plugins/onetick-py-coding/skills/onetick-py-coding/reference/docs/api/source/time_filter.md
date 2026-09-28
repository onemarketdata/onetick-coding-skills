# otp.Source.time_filter

#### ``Source.time_filter(discard_on_match=False, start_time=0, end_time=0, day_patterns='', timezone=utils.default, end_time_tick_matches=False, inplace=False)``

Filters ticks by time.

* **Parameters:**
  * **discard_on_match** (*bool* *,* *optional*) – If `True`, then ticks that match the filter will be discarded.
    Otherwise, only ticks that match the filter will be passed.
  * **start_time** (str or int or ``datetime.time``, optional) – Start time of the filter, string must be in the format `HHMMSSmmm`.
    Default value is 0.
  * **end_time** (str or int or ``datetime.time``, optional) – End time of the filter, string must be in the format `HHMMSSmmm`.
    To filter ticks for an entire day, this parameter should be set to 240000000.
    Default value is 0.
  * **day_patterns** (*list* *or* *str*) – 

    Pattern or list of patterns that determines days for which the ticks can be propagated.
    A tick can be propagated if its date matches one or more of the patterns.
    Three supported pattern formats are:
    1. `month.week.weekdays`, 0 month means any month, 0 week means any week,
       : 6 week means the last week of the month for a given weekday(s),
         weekdays are digits for each day, 0 being Sunday.
    2. `month/day`, 0 month means any month.
    3. `year/month/day`, 0 year means any year, 0 month means any month.
  * **timezone** (*str* *,* *optional*) – Timezone of the filter.
    Default value is `configuration.config.tz`
    or timezone set in the parameter of ``onetick.py.run()``.
  * **end_time_tick_matches** (*bool* *,* *optional*) – If `True`, then the end time is inclusive.
    Otherwise, the end time is exclusive.
  * **inplace** (*bool* *,* *optional*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object. Default value is `False`.
  * **self** (*Source*)
* **Returns:**
  Returns `None` if `inplace=True`.
* **Return type:**
  ``Source`` or `None`

##### Examples

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data[['PRICE', 'SIZE']]
>>> data = data.time_filter(start_time='040008000', end_time='040010000')
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time   PRICE  SIZE
0 2024-02-01 04:00:08.500354204  185.72     1
1 2024-02-01 04:00:08.506996841  185.70     1
2 2024-02-01 04:00:08.507262225  185.72     1
3 2024-02-01 04:00:09.461682352  185.72    11
```

##### SEE ALSO
**TIME_FILTER** OneTick event processor
