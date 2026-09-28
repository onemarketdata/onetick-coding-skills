# otp.Source.rename

#### ``Source.rename(columns=None, use_regex=False, fields_to_skip=None, inplace=False)``

Rename columns

* **Parameters:**
  * **columns** (*dict*) – Rules how to rename in the following format: {<column> : <new-column-name>},
    where <column> is either existing column name of str type or reference to a column,
    and <new-column-name> a new column name of str type.
  * **use_regex** (*bool*) – If true, then old-name=new-name pairs in the columns parameter are treated as regular expressions.
    This allows bulk renaming for field names. Notice that regular expressions for old names are treated as
    if both their prefix and their suffix are .\*, i.e. the prefix and suffix match any substring.
    As a result, old-name *XX* will match all of *aXX*, *aXXB*, and *XXb*, when use_regex=true.
    You can have old-name begin from ^ to indicate that .\* prefix does not apply,
    and you can have old name end at $ to indicate that .\* suffix does not apply.
    Default: false
  * **fields_to_skip** (*list* *of* *str*) – A list of regular expressions for specifying fields that should be skipped
    (i.e., not be renamed). If a field is matched by one of the specified regular expressions,
    it won’t be considered for renaming.
    Default: None
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise, method returns a new modified
    object.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

```
>>> data = otp.Ticks(X=[1], Y=[2])
>>> data = data.rename({'X': 'XX',
...                     data['Y']: 'YY'})
>>> otp.run(data)
        Time  XX  YY
0 2003-12-01   1   2
```

```
>>> data = otp.Tick(**{'X.X': 1, 'X.Y': 2})
>>> data = data.rename({r'X\.(.*)': r'\1'}, use_regex=True)
>>> otp.run(data)
        Time   X   Y
0 2003-12-01   1   2
```

```
>>> data = otp.Tick(**{'X.X': 1, 'X.Y': 2})
>>> data = data.rename({r'X\.(.*)': r'\1'}, use_regex=True, fields_to_skip=['X.Y'])
>>> otp.run(data)
        Time   X X.Y
0 2003-12-01   1   2
```

##### SEE ALSO
**RENAME** OneTick event processor
