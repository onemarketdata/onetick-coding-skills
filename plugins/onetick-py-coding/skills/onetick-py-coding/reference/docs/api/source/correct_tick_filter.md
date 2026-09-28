# otp.Source.correct_tick_filter

#### ``Source.correct_tick_filter(discard_on_match=None, as_of_time=None, inplace=False)``

This allows one to view data as it was as of any point in time.

Correction or cancellation ticks will be applied if they arrived before the time
specified by `as_of_time`,
and if they were applied to the ticks that arrived after that `as_of_time`.

* **Parameters:**
  * **discard_on_match** (*bool*) – 

    If this parameter is False (default), the output is a time series of ticks,
    with correction and cancellations that arrived after the `as_of_time` not applied to the ticks
    that arrived before or at the the `as_of_time`.

    If this parameter is True, the correct ticks, incorrect copies of which arrived before or at the `as_of_time`,
    which were corrected after `as_of_time` will be propagated.
  * **as_of_time** (``otp.datetime`` or int or str) – 

    Datetime object or integer/string in the YYYYMMDDhhmmss format.

    The cut-off time, in the timezone of the query, for the correction tick or cancellation to be applied
    (the correction or cancellation must happen before or at this cut-off time,
    or the original (corrected or canceled) tick must arrive after this cutoff time,
    for this correction or cancellation to affect the output of this filter.

    The special value NOW (stands for “as of now”) ensures that only the corrected ticks
    are being sent to the output stream.

    Special value 0 (stands for “beginning of time”) results in the propagation
    of all uncorrected ticks and correction ticks as part of the stream.

    Default value: NOW.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing.
    Otherwise, method returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Let’s find some canceled ticks first and see the time they were deleted:

```
>>> start = otp.dt(2024, 2, 1, 11, 50, 15)
>>> end   = otp.dt(2024, 2, 1, 11, 50, 15, 900_000)
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.show_hidden_ticks()
>>> data = data.where(data['TICK_STATUS'] == 1)
>>> data = data[['PRICE', 'SIZE', 'TICK_STATUS', 'DELETED_TIME']]
>>> otp.run(data, start=start, end=end)
                           Time    PRICE  SIZE  TICK_STATUS            DELETED_TIME
0 2024-02-01 11:50:15.621123654  185.695     1            1 2024-02-01 14:43:17.690
```

Now let’s use it as `as_of_time` parameter.
First let’s set it to the time *before* the tick was deleted.
We will see the deleted tick along with regular ticks:

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.correct_tick_filter(as_of_time=otp.dt('2024-02-01 14:43:17'))
>>> data = data[['PRICE', 'SIZE', 'TICK_STATUS', 'DELETED_TIME']]
>>> otp.run(data, start=start, end=end)
                           Time    PRICE  SIZE  TICK_STATUS            DELETED_TIME
0 2024-02-01 11:50:15.232051023  185.695     1            0 1969-12-31 19:00:00.000
1 2024-02-01 11:50:15.621123654  185.695     1            1 2024-02-01 14:43:17.690
2 2024-02-01 11:50:15.835322561  185.695    14            0 1969-12-31 19:00:00.000
```

Now let’s set `as_of_time` parameter to the time *after* the tick was deleted.
The deleted tick has disappeared and we see the corrected data now.

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.correct_tick_filter(as_of_time=otp.dt('2024-02-01 14:43:18'))
>>> data = data[['PRICE', 'SIZE', 'TICK_STATUS', 'DELETED_TIME']]
>>> otp.run(data, start=start, end=end)
                           Time    PRICE  SIZE  TICK_STATUS        DELETED_TIME
0 2024-02-01 11:50:15.232051023  185.695     1            0 1969-12-31 19:00:00
1 2024-02-01 11:50:15.835322561  185.695    14            0 1969-12-31 19:00:00
```

##### SEE ALSO
**CORRECT_TICK_FILTER** OneTick event processor
``show_hidden_ticks()``
``show_corrected_ticks()``
