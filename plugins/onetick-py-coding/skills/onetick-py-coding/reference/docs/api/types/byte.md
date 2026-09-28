# otp.byte

### ``class byte(value, *args, **kwargs)``

OneTick data type representing byte integer.

The size of the type is not specified and may vary across different systems.
Most commonly it’s a 1-byte type with allowed values from -128 to 127.

Note that the value is checked to be valid in constructor,
but no overflow checking is done when arithmetic operations are performed.

##### Examples

```
>>> t = otp.Tick(A=otp.byte(1))
>>> t['B'] = otp.byte(1) + 1
>>> t.schema
{'A': <class 'onetick.py.types.byte'>, 'B': <class 'onetick.py.types.byte'>}
```

Note that arithmetic operations may result in overflow.
Here we get 127 instead of -129.

```
>>> t = otp.Tick(A=otp.byte(-128) - 1)
>>> otp.run(t)
        Time    A
0 2003-12-01  127
```
