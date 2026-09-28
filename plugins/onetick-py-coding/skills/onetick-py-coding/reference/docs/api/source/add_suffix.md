# otp.Source.add_suffix

#### ``Source.add_suffix(suffix, inplace=False, columns=None, ignore_columns=None)``

Adds suffix to all column names (except **TIMESTAMP** (or **Time**) special column).

* **Parameters:**
  * **suffix** (*str*) – String suffix to add to all columns.
  * **inplace** (*bool*) – The flag controls whether operation should be applied inplace or not.
    If `inplace=True`, then it returns nothing. Otherwise method returns a new modified
    object.
  * **columns** (*list* **[*str* *]* *,* *optional*) – If set, only selected columns will be updated with suffix. Can’t be used with `ignore_columns` parameter.
  * **ignore_columns** (*list* **[*str* *]* *,* *optional*) – If set, selected columns won’t be updated with suffix. Can’t be used with `columns` parameter.
  * **self** (*Source*)
* **Return type:**
  ``Source`` or `None`

##### Examples

Add suffix  *\_test* to all columns (note that column **Time** is not renamed):

```
>>> data = otp.DataSource(db='US_COMP_SAMPLE', tick_type='TRD', symbols='AAPL')
>>> data = data[['PRICE', 'SIZE']][:3]
>>> data = data.add_suffix('_test')
>>> otp.run(data, date=otp.dt(2024, 2, 1))
                           Time  PRICE_test  SIZE_test
0 2024-02-01 04:00:00.008283417      186.50          6
1 2024-02-01 04:00:00.008290927      185.59          1
2 2024-02-01 04:00:00.008291153      185.49        107
```

Parameter `columns` specifies columns to be updated with suffix:

```
>>> data = otp.Tick(A=1, B=2, C=3, D=4, E=5)
>>> data = data.add_suffix('_test', columns=['A', 'B', 'C'])
>>> otp.run(data)
        Time  A_test  B_test  C_test  D  E
0 2003-12-01       1       2       3  4  5
```

Parameter `ignore_columns` specifies columns to ignore:

```
>>> data = otp.Tick(A=1, B=2, C=3, D=4, E=5)
>>> data = data.add_suffix('_test', ignore_columns=['A', 'B', 'C'])
>>> otp.run(data)
        Time  A  B  C  D_test  E_test
0 2003-12-01  1  2  3       4       5
```

Parameters `columns` and `ignore_columns` can’t be used at the same time:

```
>>> data = otp.Tick(A=1, B=2, C=3, D=4, E=5)
>>> data.add_suffix('_test', columns=['B', 'C'], ignore_columns=['A'])
Traceback (most recent call last):
    ...
ValueError: It is allowed to use only one of `columns` or `ignore_columns` parameters at a time
```

Columns can’t be renamed if their resulting name will be equal to existing column name:

```
>>> data = otp.Tick(X=1, XX=2)
>>> data.add_suffix('X', columns=['X'])
Traceback (most recent call last):
    ...
AttributeError: Column XX already exists, please, use another suffix
```
