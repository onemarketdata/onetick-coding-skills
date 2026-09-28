# otp.nsectime

### *class* nsectime

Bases: ``int``

OneTick data type representing datetime with nanoseconds precision.
Can be used to specify otp.Source column type when converting columns or creating new ones.
Note that this constructor creates datetime value in GMT timezone
and doesn’t take into account the timezone with which the query is executed.

##### Examples

```
>>> t = otp.Tick(A=0)
>>> t['A'] = t['A'].apply(otp.nsectime)
>>> t['B'] = otp.nsectime(24 * 60 * 60 * 1000 * 1000 * 1000 + 2)
>>> t.schema
{'A': <class 'onetick.py.types.nsectime'>, 'B': <class 'onetick.py.types.nsectime'>}
>>> otp.run(t)
        Time                   A                             B
0 2003-12-01 1969-12-31 19:00:00 1970-01-01 19:00:00.000000002
```
