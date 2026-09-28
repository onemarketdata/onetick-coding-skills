# otp.Source.add_fields

#### ``Source.add_fields(fields, override=False, inplace=False)``

Add new columns to the source.

* **Parameters:**
  * **fields** (*dict*) – The dictionary of the names of the new fields and their values.
    The types of supported values in the dictionary are the same as in ``Source.__setitem__()``.
  * **override** (*bool*) – If *False* then exception will be raised for existing fields.
    If *True* then exception will not be raised and the field will be overridden.
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`.

##### Examples

Add new fields specified in the dictionary:

```
>>> data = otp.Tick(A=1)
>>> data = data.add_fields({
...     'D': otp.datetime(2022, 2, 2),
...     'X': 12345,
...     'Y': data['A'],
...     'Z': data['A'].astype(str) + 'abc',
... })
>>> otp.run(data)
        Time  A           D      X  Y     Z
0 2003-12-01  1  2022-02-02  12345  1  1abc
```

Parameter `override` can be used to rewrite existing fields:

```python
data = otp.Tick(A=1)
data = data.add_fields({'A': 2, 'B': 'b'}, override=True)
df = otp.run(data)
print(df)
```

```
        Time  A  B
0 2003-12-01  2  b
```

##### SEE ALSO
``Source.__setitem__()``

**ADD_FIELDS** OneTick event processor

