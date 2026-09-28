# otp.Source._\_getitem_\_

#### Source.\_\_getitem_\_(item)

Allows to express multiple things:

- access a field by name
- filter ticks by condition
- select subset of fields
- set order of fields

* **Parameters:**
  * **item** (str, ``Operation``, ``eval()``, list of str) – 
    - `str` is to access column by name or columns specified by regex.
    - `Operation` to express filter condition.
    - `otp.eval` to express filter condition based on external query
    - `list[str]` select subset of specified columns or columns specified in regexes.
    - `slice[list[str]::]` set order of columns
    - `slice[tuple[str, type]::]` type defaulting
    - `slice[:]` alias to ``Source.copy()``
    - `slice[int:int:int]` select ticks the same way as elements in python lists
  * **self** (*Source*)
* **Returns:**
  - Column if column name was specified.
  - Two sources if filtering expression or eval was provided: the first one is for ticks that pass condition
    : and the second one that do not.
* **Return type:**
  `Column`, `Source` or `tuple` of Sources

##### Examples

Access to the X column: add Y based on X

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data['Y'] = data['X'] * 2
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  2
1 2003-12-01 00:00:00.001  2  4
2 2003-12-01 00:00:00.002  3  6
```

Filtering based on expression:

```
>>> data = otp.Ticks(X=[1, 2, 3])
>>> data_more, data_less = data[(data['X'] > 2)]
>>> otp.run(data_more)
                     Time  X
0 2003-12-01 00:00:00.002  3
>>> otp.run(data_less)
                     Time  X
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
```

Filtering based on the result of another query. Another query should
have only one tick as a result with only one field (whatever it names).

```
>>> exp_to_select = otp.Ticks(WHERE=['X > 2'])
>>> data = otp.Ticks(X=[1, 2, 3], Y=['a', 'b', 'c'], Z=[.4, .3, .1])
>>> data, _ = data[otp.eval(exp_to_select)]
>>> otp.run(data)
                     Time  X  Y    Z
0 2003-12-01 00:00:00.002  3  c  0.1
```

Select subset of specified columns:

```
>>> data = otp.Ticks(X=[1, 2, 3], Y=['a', 'b', 'c'], Z=[.4, .3, .1])
>>> data = data[['X', 'Z']]
>>> otp.run(data)
                     Time  X    Z
0 2003-12-01 00:00:00.000  1  0.4
1 2003-12-01 00:00:00.001  2  0.3
2 2003-12-01 00:00:00.002  3  0.1
```

Slice with list will keep all columns, but change order:

```
>>> data=otp.Tick(Y=1, X=2, Z=3)
>>> otp.run(data)
        Time  Y  X  Z
0 2003-12-01  1  2  3
>>> data = data[['X', 'Y']:]
>>> otp.run(data)
        Time  X  Y  Z
0 2003-12-01  2  1  3
```

Slice can be used as short-cut for ``Source.copy()``:

```
>>> data[:] 
<onetick.py.sources.ticks.Tick object at ...>
```

Slices can use integers.
In this case ticks are selected the same way as elements in python lists.

```
>>> data = otp.Ticks({'A': [1, 2, 3, 4, 5]})
```

Select first 3 ticks:

```
>>> otp.run(data[:3])
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
2 2003-12-01 00:00:00.002  3
```

Skip first 3 ticks:

```
>>> otp.run(data[3:])
                     Time  A
0 2003-12-01 00:00:00.003  4
1 2003-12-01 00:00:00.004  5
```

Select last 3 ticks:

```
>>> otp.run(data[-3:])
                     Time  A
0 2003-12-01 00:00:00.002  3
1 2003-12-01 00:00:00.003  4
2 2003-12-01 00:00:00.004  5
```

Skip last 3 ticks:

```
>>> otp.run(data[:-3])
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.001  2
```

Skip first and last tick:

```
>>> otp.run(data[1:-1])
                     Time  A
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.002  3
2 2003-12-01 00:00:00.003  4
```

Select every second tick:

```
>>> otp.run(data[::2])
                     Time  A
0 2003-12-01 00:00:00.000  1
1 2003-12-01 00:00:00.002  3
2 2003-12-01 00:00:00.004  5
```

Select every second tick, not including first and last tick:

```
>>> otp.run(data[1:-1:2])
                     Time  A
0 2003-12-01 00:00:00.001  2
1 2003-12-01 00:00:00.003  4
```

Regular expressions can be used to select fields too:

```
>>> data = otp.Tick(A=1, AA=2, AB=3, B=4, BB=5, BA=6)
>>> otp.run(data['A.*'])
        Time  A  AA  AB  BA
0 2003-12-01  1   2   3   6
```

Note that by default pattern is matched in any position of the string.
Use characters ^ and $ to specify start and end of the string:

```
>>> otp.run(data['^A'])
        Time  A  AA  AB
0 2003-12-01  1   2   3
```

Several regular expressions can be specified too:

```
>>> otp.run(data[['^A+$', '^B+$']])
        Time  A  AA  B  BB
0 2003-12-01  1   2  4   5
```

##### SEE ALSO
``Source.table()``: another and more generic way to select subset of specified columns

**PASSTHROUGH** OneTick event processor

**WHERE_CLAUSE** OneTick event processor

