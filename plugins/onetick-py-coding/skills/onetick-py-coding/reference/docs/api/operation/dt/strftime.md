# otp.Operation.dt.strftime

### ``strftime(format, timezone)``

Converts the number of nanoseconds (datetime) since 1970/01/01 GMT into
the string specified by `format` for a specified `timezone`.

* **Parameters:**
  * **format** (*str*) – 

    The format might contain any characters, but the following combinations of
    characters have special meanings

    %Y - Year (4 digits)

    %y - Year (2 digits)

    %m - Month (2 digits)

    %d - Day of month (2 digits)

    %H - Hours (2 digits, 24-hour format)

    %I - Hours (2 digits, 12-hour format)

    %M - Minutes (2 digits)

    %S - Seconds (2 digits)

    %q - Milliseconds (3 digits)

    %J - Nanoseconds (9 digits)

    %p - AM/PM (2 characters)

    %% - % character
  * **timezone** (*str* *|* *Operation* *|* *Column*) – Name of the timezone, an operation or a column with it.
    By default, the timezone of the query will be used.

##### Examples

```
>>> t = otp.Ticks(A=[otp.dt(2019, 1, 1, 1, 1, 1), otp.dt(2019, 2, 2, 2, 2, 2)])
>>> t['B'] = t['A'].dt.strftime('%d.%m.%Y')
>>> otp.run(t)[['A', 'B']]
                    A           B
0 2019-01-01 01:01:01  01.01.2019
1 2019-02-02 02:02:02  02.02.2019
```
