# otp.short

### ``class short(value, *args, **kwargs)``

OneTick data type representing short integer.

The size of the type is not specified and may vary across different systems.
Most commonly it’s a 2-byte type with allowed values from -32768 to 32767.

Note that the value is checked to be valid in constructor,
but no overflow checking is done when arithmetic operations are performed.

##### Examples

```
>>> t = otp.Tick(A=otp.short(1))
>>> t['B'] = otp.short(1) + 1
>>> t.schema
{'A': <class 'onetick.py.types.short'>, 'B': <class 'onetick.py.types.short'>}
```

Note that arithmetic operations may result in overflow.
Here we get 32767 instead of -32769.

```
>>> t = otp.Tick(A=otp.short(-32768) - 1)
>>> otp.run(t)
        Time      A
0 2003-12-01  32767
```
