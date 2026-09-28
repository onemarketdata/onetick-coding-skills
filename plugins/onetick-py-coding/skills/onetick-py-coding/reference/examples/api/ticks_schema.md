# onetick.py and OneTick types mapping

- OneTick `long` type maps into the `int` python type in onetick.py
- OneTick `int` type maps into the `int` python type in onetick.py
- OneTick `double` type maps into the `float` python type in onetick.py
- OneTick `float` type maps into the `float` python type in onetick.py
- OneTick `string` type maps into the `str` python type in onetick.py
- OneTick `uint32` type maps into the `otp.uint` onetick.py type
- OneTick `uint64` type maps into the `otp.ulong` onetick.py type
- OneTick `uint16` type maps into the `otp.short` onetick.py type
- OneTick `int8` type maps into the `otp.byte` onetick.py type
- OneTick `msec` type maps into the `otp.msectime` onetick.py type
- OneTick `nsec` type maps into the `otp.nsectime` onetick.py type

Note that OneTick does not have boolean type, it uses `double` type instead.

All timestamp in onetick.py by default has the `otp.nsectime`.