# otp.Source.mkt_activity

#### ``Source.mkt_activity(calendar_name=None, inplace=False)``

Adds a string field named **MKT_ACTIVITY** to each tick in the input tick stream.

The value of this field is set to the union of session flags that apply for the security at the time of the tick,
as specified in the calendar sections of the reference database (see Reference Database Guide).

Session flags may differ between databases,
but the following letters have reserved meaning: L - half day, H - holiday, W - weekend, R - regular.

The calendar can either be specified explicitly by name
(the *CALENDAR* sections of the reference database are assumed to contain a calendar with such a name),
or default to the security- or exchange-level calendars for the queried symbol
(the *SYMBOL_CALENDAR* or *EXCH_CALENDAR* sections of the reference database).

The latter case requires a non-zero symbol date to be specified for queried symbols
(see parameter `symbol_date` in ``otp.run``).

* **Parameters:**
  * **calendar_name** (str, ``Column``) – 

    The calendar name to choose for the respective calendar from the *CALENDAR* sections of the reference database.
    It can be a string constant or the name of the field with per-tick calendar name.

    When this parameter is not specified,
    default security- or exchange-level calendars configured for the queried database and symbol are used
    (but symbol date must be specified in this case).
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

#### NOTE
When applying ``mkt_activity()`` to aggregated data,
please take into account that session flags may change during the aggregation bucket.
The field **MKT_ACTIVITY**, in this case, will represent the session flags at the time assigned to the bucket,
which may be different from the session flags at some times during the bucket.

##### Examples

By default, security- or exchange-level calendars configured for the queried database and symbol are used
(but symbol date must be specified in this case):

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data[['PRICE', 'SIZE']][:5]
>>> data = data.mkt_activity()
>>> otp.run(data, date=otp.date(2024, 2, 1), symbol_date=otp.date(2024, 2, 1))
                           Time   PRICE  SIZE MKT_ACTIVITY
0 2024-02-01 04:00:00.008283417  186.50     6           Rb
1 2024-02-01 04:00:00.008290927  185.59     1           Rb
2 2024-02-01 04:00:00.008291153  185.49   107           Rb
3 2024-02-01 04:00:00.010381671  185.49     1           Rb
4 2024-02-01 04:00:00.011224206  185.50     2           Rb
```

Otherwise, parameter `calendar_name` must be specified:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data[['PRICE', 'SIZE']][:5]
>>> data = data.mkt_activity(calendar_name='CLOUD_DB_US_COMP')
>>> otp.run(data, date=otp.date(2024, 2, 1))
                           Time   PRICE  SIZE MKT_ACTIVITY
0 2024-02-01 04:00:00.008283417  186.50     6           Rb
1 2024-02-01 04:00:00.008290927  185.59     1           Rb
2 2024-02-01 04:00:00.008291153  185.49   107           Rb
3 2024-02-01 04:00:00.010381671  185.49     1           Rb
4 2024-02-01 04:00:00.011224206  185.50     2           Rb
```

Parameter `calendar_name` can also be specified as a column.
In this case calendar name can be different for each tick:

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')  
>>> data['CALENDAR_NAME'] = ...                                               
>>> data = data.mkt_activity(calendar_name=data['CALENDAR_NAME'])             
>>> otp.run(data, date=otp.date(2024, 2, 1))                                  
```

In this example you can see how market activity status is changing during the day.
We are getting first and last tick of the group each time the type of market activity is changed.
You can see PRE_MARKET (Rb) trades before 9:30,
regular trades (R) from 9:30 to 16:00,
and POST_MARKET trades (Ra) after 16:00.

```
>>> data = otp.DataSource('US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data.mkt_activity(calendar_name='CLOUD_DB_US_COMP')
>>> data = data[['PRICE', 'SIZE', 'MKT_ACTIVITY']]
>>> first = data.first(1, bucket_interval=(data['MKT_ACTIVITY'] != data['MKT_ACTIVITY'][-1]))
>>> last = data.last(1, bucket_interval=(data['MKT_ACTIVITY'] != data['MKT_ACTIVITY'][-1]))
>>> data = otp.merge([first, last])
>>> otp.run(data, date=otp.date(2024, 2, 1))
                           Time     PRICE  SIZE MKT_ACTIVITY
0 2024-02-01 04:00:00.008283417  186.5000     6           Rb
1 2024-02-01 09:29:59.963219812  184.1000   900           Rb
2 2024-02-01 09:30:00.000961260  184.0100   302           Rr
3 2024-02-01 15:59:59.990606937  186.8312     1           Rr
4 2024-02-01 16:00:00.000287011  186.8900   100           Ra
5 2024-02-01 19:59:59.547785229  181.4500    59           Ra
```

##### SEE ALSO
**MKT_ACTIVITY** OneTick event processor
``otp.RefData``
``DB.ref_data``
