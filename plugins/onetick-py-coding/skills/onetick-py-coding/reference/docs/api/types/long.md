# otp.long

### ``class long(value, *args, **kwargs)``

OneTick data type representing signed long integer.

The size of the type is not specified and may vary across different systems.
Most commonly it’s a 8-byte type with allowed values from -2\*\*63 to 2\*\*63 - 1.

Note that the value is checked to be valid in constructor,
but no overflow checking is done when arithmetic operations are performed.

##### Examples

```
>>> t = otp.Tick(A=otp.long(1))
>>> t['B'] = otp.long(1) + 1
>>> t.schema
{'A': <class 'onetick.py.types.long'>, 'B': <class 'onetick.py.types.long'>}
```
