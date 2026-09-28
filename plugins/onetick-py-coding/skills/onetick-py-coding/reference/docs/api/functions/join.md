# otp.join

### ``join(left, right, on, how='left_outer', rprefix='RIGHT', keep_fields_not_in_schema=False, output_type_index=None)``

Joins two sources `left` and `right` based on `on` condition.

In case you willing to add prefix/suffix to all columns in one of the sources you should use
``Source.add_prefix()`` or ``Source.add_suffix()``

* **Parameters:**
  * **left** (``Source``) – left source to join
  * **right** (``Source``) – right source to join
  * **on** (``Operation`` or ‘all’ or ‘same_size’ or list of strings) – 

    If ‘all’ joins every tick from `left` with every tick from `right`.

    If ‘same_size’ and size of sources are same, joins ticks from two sources directly, else raises exception.

    If it is list of strings, then ticks with same `on` fields will be joined.

    If ``Operation`` then only ticks on which the condition evaluates to True will be joined.
  * **how** ( *'inner'* *or*  *'left_outer'*) – 

    Joining type.
    Inner join will only produce ticks that matched the `on` condition.
    Left outer join will also produce the ticks from the `left` source
    that didn’t match the condition.

    Doesn’t matter for `on='same_size'`.
  * **rprefix** (*str*) – The name of `right` data source. It will be added as prefix to overlapping columns arrived
    from right to result
  * **keep_fields_not_in_schema** (*bool*) – 

    If True - join function will try to preserve any fields of original sources that are not in the source schema,
    propagating them to output. This means a possibility of runtime error if fields are duplicating.

    If False, will remove all fields that are not in schema.
  * **output_type_index** (*int*) – Specifies index of source in sources from which type and properties of output will be taken.
    Useful when joining sources that inherited from ``Source``.
    By default output object type will be ``Source``.
* **Returns:**
  joined data
* **Return type:**
  ``Source`` or same class as `[left, right][output_type_index]`

#### NOTE
`join` does some internal optimization in case of using time-based `on` conditions. Optimization doesn’t apply
if `on` expression has functions in it. So it is recommended to use addition/subtraction number of
milliseconds (integers).

See examples for more details.

##### Examples

```
>>> d1 = otp.Ticks({'ID': [1, 2, 3], 'A': ['a', 'b', 'c']})
>>> d2 = otp.Ticks({'ID': [2, 3, 4], 'B': ['q', 'w', 'e']})
```

Outer join:

```
>>> data = otp.join(d1, d2, on=d1['ID'] == d2['ID'], how='left_outer')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.000   1  a         0
1 2003-12-01 00:00:00.001   2  b         2  q
2 2003-12-01 00:00:00.002   3  c         3  w
```

Inner join:

```
>>> data = otp.join(d1, d2, on=d1['ID'] == d2['ID'], how='inner')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.001   2  b         2  q
1 2003-12-01 00:00:00.002   3  c         3  w
```

Join all ticks:

```
>>> data = otp.join(d1, d2, on='all')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.000   1  a         2  q
1 2003-12-01 00:00:00.000   1  a         3  w
2 2003-12-01 00:00:00.000   1  a         4  e
3 2003-12-01 00:00:00.001   2  b         2  q
4 2003-12-01 00:00:00.001   2  b         3  w
5 2003-12-01 00:00:00.001   2  b         4  e
6 2003-12-01 00:00:00.002   3  c         2  q
7 2003-12-01 00:00:00.002   3  c         3  w
8 2003-12-01 00:00:00.002   3  c         4  e
```

Join same size sources:

```
>>> data = otp.join(d1, d2, on='same_size')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.000   1  a         2  q
1 2003-12-01 00:00:00.001   2  b         3  w
2 2003-12-01 00:00:00.002   3  c         4  e
```

Adding prefix to the right source for all columns:

```
>>> d_right = d2.add_prefix('right_')
>>> data = otp.join(d1, d_right, on=d1['ID'] == d_right['right_ID'])
>>> otp.run(data)
                     Time  ID  A  right_ID  right_B
0 2003-12-01 00:00:00.000   1  a         0
1 2003-12-01 00:00:00.001   2  b         2        q
2 2003-12-01 00:00:00.002   3  c         3        w
```

This condition will be optimized during run time:

```
>>> data = otp.join(d1, d2, on=(d1['ID'] == d2['ID']) & (d1['Time'] >= d2['Time']), how='left_outer')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.000   1  a         0
1 2003-12-01 00:00:00.001   2  b         2  q
2 2003-12-01 00:00:00.002   3  c         3  w
```

This condition won’t be optimized during run time since in transforms addition to time into function.
So please note, this way of using `join` is not recommended.

```
>>> data = otp.join(d1, d2, on=(d1['ID'] == d2['ID']) & (d1['Time'] >= d2['Time'] + otp.Milli(1)), how='left_outer')
>>> otp.run(data)
                     Time  ID  A  RIGHT_ID  B
0 2003-12-01 00:00:00.000   1  a         0
1 2003-12-01 00:00:00.001   2  b         2  q
2 2003-12-01 00:00:00.002   3  c         3  w
```

In such cases (adding/subtracting constants to time) adding/subtraction number of milliseconds should be done.
This example will return exactly the same result as previous one, but it will be optimized, so runtime will be
shorter.

```
>>> data = otp.join(d1, d2, on=(d1['ID'] == d2['ID']) & (d1['Time'] >= d2['Time'] + 1), how='left_outer')
>>> otp.run(data)
                         Time  ID  A  RIGHT_ID  B
    0 2003-12-01 00:00:00.000   1  a         0
    1 2003-12-01 00:00:00.001   2  b         2  q
    2 2003-12-01 00:00:00.002   3  c         3  w
```

`on` can be list of strings:

```
>>> left = otp.Ticks(A=[1, 2, 3], B=[4, 6, 7])
>>> right = otp.Ticks(A=[2, 3, 4], B=[6, 9, 8], C=[7, 2, 0])
>>> data = otp.join(left, right, on=['A', 'B'], how='inner')
>>> otp.run(data)
                         Time  A  B  C
    0 2003-12-01 00:00:00.001  2  6  7
```

Use parameter `output_type_index` to specify which input class to use to create output object.
It may be useful in case some custom user class was used as input:

```
>>> class CustomTick(otp.Tick):
...     def custom_method(self):
...         return 'custom_result'
>>> data1 = otp.Tick(A=1)
>>> data2 = CustomTick(B=2)
>>> data = otp.join(data1, data2, on='same_size', output_type_index=1)
>>> type(data)
<class 'onetick.py.functions.CustomTick'>
>>> data.custom_method()
'custom_result'
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  2
```

##### SEE ALSO
**JOIN** OneTick event processor
