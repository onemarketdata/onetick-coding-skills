# otp.raw

### ``class Raw(raw, dtype)``

Bases: ``Operation``

Data type representing raw OneTick expression.

##### Examples

```
>>> t = otp.Tick(A=1)
>>> t['A'] = '_TIMEZONE'
>>> t['B'] = otp.raw('_TIMEZONE', dtype=otp.string[64])
>>> otp.run(t, timezone='Asia/Yerevan')
        Time          A             B
0 2003-12-01  _TIMEZONE  Asia/Yerevan
```
