# otp.msectime

### *class* msectime

Bases: ``int``

OneTick data type representing datetime with milliseconds precision.
Can be used to specify otp.Source column type when converting columns or creating new ones.
Note that this constructor creates datetime value in GMT timezone
and doesn’t take into account the timezone with which the query is executed.

##### Examples

```
>>> t = otp.Tick(A=1)
>>> t = t.table(A=otp.msectime)
>>> t['B'] = otp.msectime(2)
>>> t.schema
{'A': <class 'onetick.py.types.msectime'>, 'B': <class 'onetick.py.types.msectime'>}
>>> otp.run(t)
        Time                       A                       B
0 2003-12-01 1969-12-31 19:00:00.001 1969-12-31 19:00:00.002
```
