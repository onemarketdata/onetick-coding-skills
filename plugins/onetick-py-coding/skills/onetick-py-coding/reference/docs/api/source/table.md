# otp.Source.table

#### ``Source.table(inplace=False, strict=True, **schema)``

Set the OneTick and python schemas levels according to the `schema`
parameter. The `schema` should contain either (field_name -> type) pairs
or (field_name -> default value) pairs; `None` means no specified type, and
OneTick considers it’s as a double type.

Resulting ticks have the same order as in the `schema`. If only partial fields
are specified (i.e. when the `strict=False`) then fields from the `schema` have
the most left position.

* **Parameters:**
  * **inplace** (*bool*) – The flag controls whether operations should be applied inplace
  * **strict** (*bool*) – If set to `False`, all fields present in an input tick will be present in the output tick.
    If `True`, then only fields specified in the `schema`.
  * **schema** – field_name -> type or field_name -> default value pairs that should be applied on the source.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Selection case

```
>>> data = otp.Ticks(X1=[1, 2, 3],
...                  X2=[3, 2, 1],
...                  A1=["A", "A", "A"])
>>> data = data.table(X2=int, A1=str)
>>> otp.run(data)
                     Time  X2 A1
0 2003-12-01 00:00:00.000   3  A
1 2003-12-01 00:00:00.001   2  A
2 2003-12-01 00:00:00.002   1  A
```

Defining default values case (note the order)

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data = data.table(Y=0.5, strict=False)
>>> otp.run(data)
                     Time   Y   X
0 2003-12-01 00:00:00.000  0.5  1
1 2003-12-01 00:00:00.001  0.5  2
2 2003-12-01 00:00:00.002  0.5  3
```

##### SEE ALSO
``Source.schema``

``__getitem__()``: the table shortcut

**TABLE** OneTick event processor

