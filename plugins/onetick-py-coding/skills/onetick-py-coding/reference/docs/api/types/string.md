# otp.string

### *class* string

Bases: ``str``

OneTick data type representing string with length and varstring.
To set string length use `__getitem__`.
If the length is not set then the ``DEFAULT_LENGTH`` length is used by default.
In this case using `otp.string` is the same as using `str`.
If the length is set to Ellipse it represents varstring. Varstring is used for returning variably sized strings.

#### NOTE
If you try to set value with length x to string[y] and x > y, value will be truncated to y length.

##### Examples

Adding new fields with ``otp.string`` type:

```
>>> t = otp.Tick(A=otp.string`32`)
>>> t['B'] = otp.string`128`
>>> t.schema
{'A': string[32], 'B': string[128]}
>>> otp.run(t)
        Time      A      B
0 2003-12-01  HELLO  WORLD
```

Note that class ``otp.string`` is a child of python’s `str` class,
so every object passed to constructor is converted to string.
So it may not work as expected in some cases, e.g. when passing ``Operation`` objects
(because their string representation is the name of the column or OneTick expression).
In this case it’s better to use direct type conversion to get ``otp.string`` object:

```
>>> t = otp.Tick(X=otp.string`256`)
>>> t['Y'] = t['_SYMBOL_NAME'].astype(otp.string[256])
>>> t.schema
{'X': string[256], 'Y': string[256]}
>>> otp.run(t, symbols='AAAA')
        Time             X     Y
0 2003-12-01  _SYMBOL_NAME  AAAA
```

Setting the type of the existing field:

```
>>> t = otp.Tick(A='a')
>>> t = t.table(A=otp.string[10])
>>> t.schema
{'A': string[10]}
```

Example of truncation column value to set string length.

```
>>> t['A'] *= 100
>>> t['B'] = t['A'].str.len()
>>> otp.run(t)
        Time           A   B
0 2003-12-01  aaaaaaaaaa  10
```

Example of string with default length.

```
>>> t = otp.Tick(A='a')
>>> t['A'] *= 100
>>> t['B'] = t['A'].str.len()
>>> otp.run(t)
        Time                                                                 A   B
0 2003-12-01  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa  64
```

Setting Ellipsis as length represents varstring.

```
>>> t = otp.Tick(A='a')
>>> t = t.table(A=otp.string[...])
>>> t.schema
{'A': varstring}
```

Varstring length is multiplied.

```
>>> t['A'] *= 65
>>> t['B'] = t['A'].str.len()
>>> otp.run(t)
        Time                                                                  A   B
0 2003-12-01  aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa  65
```

otp.varstring is a shortcut:

```
>>> t = otp.Tick(A='a')
>>> t = t.table(A=otp.varstring)
>>> t.schema
{'A': varstring}
```

* **Variables:**
  **DEFAULT_LENGTH** (*int*) – default length of the string when the length is not specified

#### DEFAULT_LENGTH *= 64*
