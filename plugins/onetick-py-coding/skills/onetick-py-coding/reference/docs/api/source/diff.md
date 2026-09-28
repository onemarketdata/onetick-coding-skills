# otp.Source.diff

#### ``Source.diff(other, fields=None, ignore=False, output_ignored_fields=None, show_only_fields_that_differ=None, show_matching_ticks=None, show_all_ticks=False, non_decreasing_value_fields=None, threshold=None, left_prefix='L', right_prefix='R', drop_index=True)``

Compare two time-series.

A tick from the first time series is considered to match a tick from the second time series
if both ticks have identical `non_decreasing_value_fields` values
and matching values of all `fields` that are present in both ticks and are not listed as the fields to be ignored.

A field is considered to match another field
if both fields have identical names, comparable types, and identical values.

Field names in an output tick are represented as <source_name>.<field_name>.

* **Parameters:**
  * **fields** (*str* *|* *list* **[*str* *]*  *|* *None*) – List of fields to be used or ignored while comparing input time series.
    By default, if this value is not set, all fields are used in comparison.
  * **ignore** (*bool*) – If True, fields specified in the `fields` parameter are ignored while comparing the input time series.
    Otherwise, by default, only the fields that are specified in the `fields` parameter are used in comparison.
    If the `fields` parameter is empty, the value of this parameter is ignored.
  * **output_ignored_fields** (*bool* *|* *None*) – If False, the fields which are not used in comparison are excluded from the output.
    Otherwise, by default, all fields are propagated.
  * **show_only_fields_that_differ** (*bool* *|* *None*) – If True (default), the method outputs only the tick fields the values of which were different.
    But if `output_ignored_fields` is set to True, then ignored fields are still propagated.
  * **show_matching_ticks** (*bool* *|* *None*) – If True, the output of this method consists of matched ticks from both input time series
    instead of unmatched ticks.
    The output tick timestamp is equal to the earliest timestamp of its corresponding input ticks.
    Default value is False.
  * **show_all_ticks** (*bool*) – 

    If specified, the output of this EP consists of both matched and unmatched ticks from both input time series.
    `MATCH_STATUS` field will be added to the output tick with the possible values of:
    * `0` - different ticks
    * `1` - matching ticks
    * `2` - tick from one source only

    Default: `False`
  * **non_decreasing_value_fields** (*str* *|* *list* **[*str* *]*  *|* *None*) – List of *non-decreasing* value fields to be used for matching.
    If value of this parameter is **TIMESTAMP** (default), it compares two time series based on tick timestamp.
    If other field is specified, a field named <source_name>.<TIMESTAMP>
    will be added to the tick whose value equals the tick’s primary timestamp.
  * **threshold** (*int* *|* *None*) – Specifies the number of first diff ticks to propagate.
    By default all such ticks are propagated.
  * **left_prefix** (*str*) – The prefix used in the output for fields from the left source.
  * **right_prefix** (*str*) – The prefix used in the output for fields from the right source.
  * **drop_index** (*bool*) – If False, the output tick will also carry the position(s) of input tick(s)
    in input time series in field <source_name>.INDEX for each source.
    The lowest position number is 1.
    If True then <source_name>.INDEX fields will not be included in the output.
  * **self** (*Source*)
  * **other** (*Source*)
* **Return type:**
  ``Source``

#### NOTE
The first source is considered to be the base for starting the matching process.
If a match is found, all ticks in the second source that occur before the first matched tick
are considered different from those in the first source.
The subsequent searches for the matches in the second source will begin after this latest matched tick.
The ticks from the second source that are between the found matches
are considered different from the ticks in the first source.

##### Examples

Print all ticks that have any unmatched fields:

```
>>> t = otp.Ticks(A=[1, 2], B=[0, 0])
>>> q = otp.Ticks(A=[1, 3], B=[0, 0])
>>> data = t.diff(q)
>>> otp.run(data)
                     Time  L.A  R.A
0 2003-12-01 00:00:00.001    2    3
```

Also show fields that were not different:

```
>>> t = otp.Ticks(A=[1, 2], B=[0, 0])
>>> q = otp.Ticks(A=[1, 3], B=[0, 0])
>>> data = t.diff(q, show_only_fields_that_differ=False)
>>> otp.run(data)
                     Time  L.A  L.B  R.A  R.B
0 2003-12-01 00:00:00.001    2    0    3    0
```

Change prefixes for output fields:

```
>>> t = otp.Ticks(A=[1, 2], B=[0, 0])
>>> q = otp.Ticks(A=[1, 3], B=[0, 0])
>>> data = t.diff(q, left_prefix='LEFT', right_prefix='RIGHT')
>>> otp.run(data)
                     Time  LEFT.A  RIGHT.A
0 2003-12-01 00:00:00.001       2        3
```

If there are several matching ticks then only the first will be matched:

```python
t = otp.Ticks(A=[1, 1, 1, 1, 1], B=[1, 2, 3, 4, 5], offset=[0, 0, 1000, 2000, 2000])
q = otp.Ticks(A=[1, 1, 1, 1, 1], B=[3, 4, 5, 6, 7], offset=[0, 1000, 1000, 2000, 2000])
data = t.diff(q, fields=['A'], show_matching_ticks=True, output_ignored_fields=True)
print(otp.run(data))
```

```
                 Time  L.A  L.B  R.A  R.B
0 2003-12-01 00:00:00    1    1    1    3
1 2003-12-01 00:00:01    1    3    1    4
2 2003-12-01 00:00:02    1    4    1    6
3 2003-12-01 00:00:02    1    5    1    7
```

Showing diff for every tick with `show_all_ticks` parameter:

```python
t = otp.Ticks(A=[1, 2, 3], B=[0, 0, 1])
q = otp.Ticks(A=[1, 3], B=[0, 0])
data = t.diff(q, show_all_ticks=True)
print(otp.run(data))
```

```
                     Time  MATCH_STATUS  L.A  R.A  L.B
0 2003-12-01 00:00:00.000             1    0    0    0
1 2003-12-01 00:00:00.001             0    2    3    0
2 2003-12-01 00:00:00.002             2    3    0    1
```

##### SEE ALSO
**DIFF** OneTick event processor
