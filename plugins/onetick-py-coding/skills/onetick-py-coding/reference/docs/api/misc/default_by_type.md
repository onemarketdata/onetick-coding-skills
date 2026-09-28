# otp.default_by_type

### ``default_by_type(dtype)``

Get default value by OneTick type.

* **Parameters:**
  **dtype** – one of onetick-py base types

##### Examples

```
>>> otp.default_by_type(float)
nan
>>> otp.default_by_type(otp.decimal)
decimal(0)
>>> otp.default_by_type(int)
0
>>> otp.default_by_type(otp.ulong)
ulong(0)
>>> otp.default_by_type(otp.uint)
uint(0)
>>> otp.default_by_type(otp.short)
short(0)
>>> otp.default_by_type(otp.byte)
byte(0)
>>> otp.default_by_type(otp.nsectime)
nsectime(0)
>>> otp.default_by_type(otp.msectime)
msectime(0)
>>> otp.default_by_type(str)
''
>>> otp.default_by_type(otp.string)
string('')
>>> otp.default_by_type(otp.string[123])
string`123`
>>> otp.default_by_type(otp.varstring)
varstring('')
```
