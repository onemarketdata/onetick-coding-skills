# otp.Source.dropna

#### ``Source.dropna(how='any', subset=None, inplace=False)``

Drops ticks that contain NaN values according to the policy in the `how` parameter

* **Parameters:**
  * **how** ( *"any"* *or*  *"all"*) – 

    `any` - filters out ticks if at least one field has NaN value

    `all` - filters out ticks if all fields have NaN values.
  * **subset** (*list* *of* *str*) – list of columns to check for NaN values. If `None` then all columns are checked.
  * **inplace** (*bool*) – the flag controls whether operation should be applied inplace.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Drop ticks where **at least one** field has `nan` value.

```
>>> data = otp.Ticks([[     'X',     'Y'],
...                   [     0.0,     1.0],
...                   [ otp.nan,     2.0],
...                   [     4.0, otp.nan],
...                   [ otp.nan, otp.nan],
...                   [     6.0,    7.0]])
>>> data = data.dropna()
>>> otp.run(data)[['X', 'Y']]
    X   Y
0 0.0 1.0
1 6.0 7.0
```

Drop ticks where **all** fields have `nan` values.

```
>>> data = otp.Ticks([[     'X',     'Y'],
...                   [     0.0,     1.0],
...                   [ otp.nan,     2.0],
...                   [     4.0, otp.nan],
...                   [ otp.nan, otp.nan],
...                   [     6.0,    7.0]])
>>> data = data.dropna(how='all')
>>> otp.run(data)[['X', 'Y']]
    X   Y
0 0.0 1.0
1 NaN 2.0
2 4.0 NaN
3 6.0 7.0
```

Drop ticks where **all** fields in **subset** of columns have `nan` values.

```
>>> data = otp.Ticks([[     'X',     'Y',    'Z'],
...                   [     0.0,     1.0, otp.nan],
...                   [ otp.nan,     2.0, otp.nan],
...                   [     4.0, otp.nan, otp.nan],
...                   [ otp.nan, otp.nan, otp.nan],
...                   [     6.0,     7.0, otp.nan]])
>>> data = data.dropna(how='all', subset=['X', 'Y'])
>>> otp.run(data)[['X', 'Y', 'Z']]
    X   Y   Z
0 0.0 1.0 NaN
1 NaN 2.0 NaN
2 4.0 NaN NaN
3 6.0 7.0 NaN
```
