# otp.ulong

### ``class ulong(value, *args, **kwargs)``

OneTick data type representing unsigned long integer.

The size of the type is not specified and may vary across different systems.
Most commonly it’s a 8-byte type with allowed values from 0 to 2\*\*64 - 1.

Note that the value is checked to be valid in constructor,
but no overflow checking is done when arithmetic operations are performed.

##### Examples

```
>>> t = otp.Tick(A=otp.ulong(1))
>>> t['B'] = otp.ulong(1) + 1
>>> t.schema
{'A': <class 'onetick.py.types.ulong'>, 'B': <class 'onetick.py.types.ulong'>}
```

Note that arithmetic operations may result in overflow.
Here we get 2\*\*64 - 1 instead of -1.

```python
t = otp.Tick(A=otp.ulong(0) - 1)
df = otp.run(t)
print(df)
```

```
        Time                     A
0 2003-12-01  18446744073709551615
```
