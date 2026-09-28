# otp.Source.process_by_group

#### ``Source.process_by_group(process_source_func, group_by=None, source_name=None, num_threads=None, query_parameters=None, symbol_name_field=None, out_of_order_output_tick_policy=None, added_field_name_suffix=None, inplace=False)``

Groups data by `group_by` and run `process_source_func` for each group and merge outputs for every group.
Note `process_source_func` will be converted to Onetick object and passed to query,
that means that python callable will be called only once.

* **Parameters:**
  * **process_source_func** (*callable*) – `process_source_func` should take ``Source`` apply necessary logic and return it
    or tuple of ``Source`` in this case all of them should have a common root that is the
    input ``Source``.
    The number of sources returned by this method is the same as the number of sources
    returned by `process_source_func`.
  * **group_by** (*list*) – 

    A list of field names to group input ticks by.

    If group_by is None then no group_by fields are defined
    and logic of `process_source_func` is applied to all input ticks
    at once
  * **source_name** (*str*) – A name for the source that represents all of group_by sources. Can be passed here or as a name
    of the inner sources; if passed by both ways, should be consistent
  * **num_threads** (*int*) – If specified and not zero, turns on asynchronous processing mode
    and specifies number of threads to be used for processing input ticks.
    If this parameter is not specified or zero, then input ticks are processed synchronously.
  * **query_parameters** (*dict* *,* *optional*) – Dict of parameters names and values of the query to be executed returned by `process_source_func`.
  * **symbol_name_field** (*str* *,* *optional*) – If the parameter value is not empty, the unbound symbol name of the called query is overwritten
    by the value from the field specified in this parameter. If the field does not contain a database name,
    it is taken from the tick type (if possible) or the **LOCAL::** database name is used.
    If this parameter is not set, **\_SYMBOL_NAME** is used, in case if the latter is empty,
    the **\_EMPTY** symbol is used.
  * **added_field_name_suffix** (*str* *,* *optional*) – The suffix for the names of appended key field names.
  * **out_of_order_output_tick_policy** (*str* *,* *optional*) – 

    Specifies policy for out of order ticks, produced by created groups.
    Possible values are: **throw_exception** and **discard_tick**.

    Default: **throw_exception**
  * **inplace** (*bool*) – If True - nothing will be returned and changes will be applied to current query
    otherwise changes query will be returned.
    Error is raised if `inplace` is set to True
    and multiple sources returned by `process_source_func`.
  * **self** (*Source*)
* **Return type:**
  ``Source``, tuple`[`Source``] or None

##### Examples

```
>>> d = otp.Ticks(X=[1, 1, 2, 2],
...               Y=[1, 2, 3, 4])
>>> def func(source):
...     return source.first()
>>> res = d.process_by_group(func, group_by=['X'])
>>> otp.run(res)[["X", "Y"]]
   X  Y
0  1  1
1  2  3
```

Set asynchronous processing:

```
>>> res = d.process_by_group(func, group_by=['X'], num_threads=2)
>>> otp.run(res)[['X', 'Y']]
   X  Y
0  1  1
1  2  3
```

Return multiple outputs, each with unique grouping logic:

```
>>> d = otp.Ticks(X=[1, 1, 2, 2],
...               Y=[1, 2, 1, 3])
>>> def func(source):
...     source['Z'] = source['X']
...     source2 = source.copy()
...     source = source.first()
...     source2 = source2.last()
...     return source, source2
>>> res1, res2 = d.process_by_group(func, group_by=['Y'])
>>> df1 = otp.run(res1)
>>> df2 = otp.run(res2)
>>> df1[['X', 'Y', 'Z']]
   X  Y  Z
0  1  1  1
1  1  2  1
2  2  3  2
>>> df2[['X', 'Y', 'Z']]
   X  Y  Z
0  1  2  1
1  2  1  2
2  2  3  2
```

##### SEE ALSO
**GROUP_BY** OneTick event processor
