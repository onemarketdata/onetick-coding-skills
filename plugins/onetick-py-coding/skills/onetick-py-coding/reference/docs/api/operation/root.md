# otp.Operation

### *class* Operation

Bases: ``object``

``Source`` column operation container.

This is the object you get when applying most operations on ``Column``
or on other operations.
Eventually you can add a new column using the operation you got or pass it as a parameter
to some functions.

##### Examples

```
>>> t = otp.Tick(A=1)
>>> t['A']
Column(A, <class 'int'>)
>>> t['A'] / 2
Operation((A) / (2))
>>> t['B'] = t['A'] / 2
>>> t['B']
Column(B, <class 'float'>)
```

### *class* Column

Bases: ``Operation``

``Source`` column container.

This is the object you get when using ``__getitem__()``.
You can use this object everywhere where ``Operation`` object can be used.

##### Examples

```
>>> t = otp.Tick(A=1)
>>> t['A']
Column(A, <class 'int'>)
```
