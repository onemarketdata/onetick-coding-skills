# otp.date

### ``class date(first_arg, month=None, day=None)``

Bases: ``datetime``

Class `date` is used for representing date in onetick-py.
It can be used both when specifying start and end time for queries and
in column operations with ``onetick.py.Source``.

* **Parameters:**
  * **first_arg** (int, str, ``otp.datetime``, ``otp.date``                `pandas.Timestamp`, ``datetime.datetime``, ``datetime.date``) – If month and day arguments are specified, first argument will be considered as year.
    Otherwise, first argument will be converted to otp.date.
  * **month** (*int*) – Number between 1 and 12.
  * **day** (*int*) – Number between 1 and 31.

#### NOTE
Class ``otp.date`` share many methods
that classes `pandas.Timestamp` and ``datetime.date`` have,
but these objects are not fully interchangeable.
Class ``otp.date`` should work in all onetick-py methods and classes,
other classes should work too if documented,
and may even work when not documented, but the users should not count on it.

##### Examples

`Datetime guide`.
