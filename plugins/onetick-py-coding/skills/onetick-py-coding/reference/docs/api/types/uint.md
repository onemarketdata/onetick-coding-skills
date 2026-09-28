# otp.uint

### ``class uint(value, *args, **kwargs)``

OneTick data type representing unsigned integer.

The size of the type is not specified and may vary across different systems.
Most commonly it’s a 4-byte type with allowed values from 0 to 2\*\*32 - 1.

Note that the value is checked to be valid in constructor,
but no overflow checking is done when arithmetic operations are performed.

##### Examples

```
>>> t = otp.Tick(A=otp.uint(1))
>>> t['B'] = otp.uint(1) + 1
>>> t.schema
{'A': <class 'onetick.py.types.uint'>, 'B': <class 'onetick.py.types.uint'>}
```

Note that arithmetic operations may result in overflow.
Here we get 2\*\*32 - 1 instead of -1.

```python
t = otp.Tick(A=otp.uint(0) - 1)
df = otp.run(t)
print(df)
```

```
        Time           A
0 2003-12-01  4294967295
```
