# otp.dt (otp.datetime)

### ``class datetime(first_arg, month=None, day=None, hour=None, minute=None, second=None, microsecond=None, nanosecond=None, *, tzinfo=None, tz=None)``

Bases: `AbstractTime`

Class ``otp.datetime`` is used for representing date with time in onetick-py.
It can be used both when specifying start and end time for queries and
in column operations with ``onetick.py.Source``.
`Datetime offset objects` (e.g. otp.Nano, otp.Day)
can be added to or subtracted from otp.datetime object.

* **Parameters:**
  * **first_arg** (int, str, ``otp.datetime``, ``otp.date``                `pandas.Timestamp`, ``datetime.datetime``) – If month, day and other parts of date are specified,
    first argument will be considered as year.
    Otherwise, first argument will be converted to ``otp.datetime``.
  * **month** (*int*) – Number between 1 and 12.
  * **day** (*int*) – Number between 1 and 31.
  * **hour** (*int* *,* *default=0*) – Number between 0 and 23.
  * **minute** (*int* *,* *default=0*) – Number between 0 and 59.
  * **second** (*int* *,* *default=0*) – Number between 0 and 59.
  * **microsecond** (*int* *,* *default=0*) – Number between 0 and 999999.
  * **nanosecond** (*int* *,* *default=0*) – Number between 0 and 999.
  * **tzinfo** (``datetime.tzinfo``) – Timezone object.
  * **tz** (*str*) – Timezone name.

#### NOTE
Class ``otp.datetime`` share many methods
that classes `pandas.Timestamp` and ``datetime.datetime`` have,
but these objects are not fully interchangeable.
Class ``otp.datetime`` should work in all onetick-py methods and classes,
other classes should work too if documented,
and may even work when not documented, but the users should not count on it.

##### Examples

Initialization by ``datetime.datetime`` class from standard library:

```
>>> otp.datetime(datetime.datetime(2019, 1, 1, 1))
2019-01-01 01:00:00
```

Initialization by `pandas.Timestamp` class:

```
>>> otp.datetime(pd.Timestamp(2019, 1, 1, 1))
2019-01-01 01:00:00
```

Initialization by int timestamp:

```
>>> otp.datetime(1234567890)
1970-01-01 00:00:01.234567890
```

Initialization by params with nanoseconds:

```
>>> otp.datetime(2019, 1, 1, 1, 2, 3, 4, 5)
2019-01-01 01:02:03.000004005
```

Initialization by string:

```
>>> otp.datetime('2019/01/01 1:02')
2019-01-01 01:02:00
```

otp.dt is the alias for otp.datetime:

```
>>> otp.dt(2019, 1, 1)
2019-01-01 00:00:00
```

##### SEE ALSO
`Datetime offset objects`.

#### ``property start``

#### ``property end``

#### ``replace(**kwargs)``

Replace parts of otp.datetime object.

* **Parameters:**
  * **year** (*int* *,* *optional*)
  * **month** (*int* *,* *optional*)
  * **day** (*int* *,* *optional*)
  * **hour** (*int* *,* *optional*)
  * **minute** (*int* *,* *optional*)
  * **second** (*int* *,* *optional*)
  * **microsecond** (*int* *,* *optional*)
  * **nanosecond** (*int* *,* *optional*)
  * **tzinfo** (*tz-convertible* *,* *optional*)
* **Returns:**
  **result** – Timestamp with fields replaced.
* **Return type:**
  ``otp.datetime``

##### Examples

```
>>> ts = otp.datetime(2022, 2, 24, 3, 15, 54, 999, 1)
>>> ts
2022-02-24 03:15:54.000999001
>>> ts.replace(year=2000, month=2, day=2, hour=2, minute=2, second=2, microsecond=2, nanosecond=2)
2000-02-02 02:02:02.000002002
```

#### ``property tz``

#### ``property tzinfo``

#### ``property hour``

#### ``property minute``

#### ``property second``

#### ``property microsecond``

#### ``property nanosecond``

#### ``static now(tz=None)``

Will return ``otp.datetime`` object
with timestamp at the moment of calling this function.
Not to be confused with function ``otp.now`` which can only add column
with current timestamp to the ``otp.Source`` when running the query.

* **Parameters:**
  **tz** (*str* *or* *timezone object* *,* *default None*) – Timezone to localize to.

#### ``weekday()``

Return the day of the week as an integer, where Monday is 0 and Sunday is 6.

* **Returns:**
  Day of the week (0=Monday, 1=Tuesday, …, 6=Sunday).
* **Return type:**
  `int`

##### Examples

```
>>> otp.datetime(2026, 1, 23).weekday()  # Friday
4
>>> otp.datetime(2026, 1, 25).weekday()  # Sunday
6
>>> otp.datetime(2026, 1, 19).weekday()  # Monday
0
```

#### ``classmethod strptime(date_string, format, *, tz=None, tzinfo=None)``

Parse a string to datetime according to a format.

* **Parameters:**
  * **date_string** (*str*) – The string to parse.
  * **format** (*str*) – The format string for parsing.
  * **tz** (*str* *,* *optional*) – Timezone name.
  * **tzinfo** (``datetime.tzinfo``, optional) – Timezone object.
* **Returns:**
  The parsed datetime object.
* **Return type:**
  ``otp.datetime``

##### Examples

```
>>> otp.datetime.strptime('2026-01-23 14:30:00', '%Y-%m-%d %H:%M:%S')
2026-01-23 14:30:00
>>> otp.datetime.strptime('2026-01-23 14:30:00', '%Y-%m-%d %H:%M:%S', tz='GMT')
2026-01-23 14:30:00+00:00
```

* **Raises:**
  **ValueError** – If the string cannot be parsed according to the format,
      or if both `tz` and `tzinfo` are specified.

#### \_\_add_\_(other)

Add `datetime offset` to otp.datetime.

* **Parameters:**
  **other** (`datetime offset`, ``otp.datetime``) – object to add
* **Returns:**
  **result** – return ``otp.datetime``
  if otp.Nano or another `datetime offset` object was passed as an argument,
  or `pandas.Timedelta` object if ``otp.datetime``
  was passed as an argument.
* **Return type:**
  ``otp.datetime``, `pandas.Timedelta`

##### Examples

```
>>> otp.datetime(2022, 2, 24) + otp.Nano(1)
2022-02-24 00:00:00.000000001
```

#### \_\_sub_\_(other)

Subtract `datetime offset` from otp.datetime.

* **Parameters:**
  **other** (`datetime offset`, ``otp.datetime``) – object to subtract
* **Returns:**
  **result** – return datetime if otp.Nano or another `datetime offset`
  object was passed as an argument,
  or `pandas.Timedelta` object if ``otp.datetime``
  was passed as an argument.
* **Return type:**
  ``otp.datetime``, `pandas.Timedelta`

##### Examples

```
>>> otp.datetime(2022, 2, 24) - otp.Nano(1)
2022-02-23 23:59:59.999999999
```

#### ``tz_localize(tz)``

Localize a timezone-naive datetime object to the specified timezone

* **Parameters:**
  **tz** (*str* *or* *tzinfo*) – timezone to localize datetime object into
* **Returns:**
  **result** – localized datetime object
* **Return type:**
  ``otp.datetime``

##### Examples

```
>>> d = otp.datetime(2021, 6, 3)
>>> d.tz_localize("EST5EDT")
2021-06-03 00:00:00-04:00
```

#### ``tz_convert(tz)``

Convert a timezone-aware datetime object to a different timezone

* **Parameters:**
  **tz** (*str* *or* *tzinfo*) – timezone to convert datetime object into
* **Returns:**
  **result** – converted datetime object
* **Return type:**
  ``otp.datetime``

##### Examples

```
>>> d = otp.datetime(2021, 6, 3, tz="EST5EDT")
>>> d.tz_convert("Europe/Moscow")
2021-06-03 07:00:00+03:00
```

#### ``to_operation(timezone=None)``

Convert ``otp.datetime`` object to
``otp.Operation``

* **Parameters:**
  **timezone** (*Operation*) – Can be used to specify timezone as an Operation.

##### Examples

```
>>> t = otp.Ticks(TZ=['EST5EDT', 'GMT'])
>>> t['DT'] = otp.dt(2022, 1, 1).to_operation(timezone=t['TZ'])
>>> otp.run(t, timezone='GMT')[['TZ', 'DT']]
        TZ                  DT
0  EST5EDT 2022-01-01 05:00:00
1      GMT 2022-01-01 00:00:00
```
