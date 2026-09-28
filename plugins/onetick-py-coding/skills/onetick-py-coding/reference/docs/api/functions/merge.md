# otp.merge

### ``merge(sources, align_schema=True, symbols=None, identify_input_ts=False, presort=adaptive, concurrency=default, batch_size=default, output_type_index=None, add_symbol_index=False, separate_db_name=False, added_field_name_suffix='', stabilize_schema=adaptive, enforce_order=False, symbol_date=None)``

Merges ticks from the `sources` into a single output ordered by the timestamp.

* **Parameters:**
  * **sources** (*list*) – List of sources to merge
  * **align_schema** (*bool*) – If set to True, then table is added right after merge.
    We recommended to keep True to prevent problems with
    different tick schemas. Default: True
  * **symbols** (str, list of str or functions, ``Source``, `onetick.query.GraphQuery`) – Symbol(s) to run the query for passed as a string, a list of strings, or as a “symbols” query which results
    include the `SYMBOL_NAME` column. The start/end times for the
    symbols query will taken from the ``run()`` params.
    See `symbols` for more details.
  * **identify_input_ts** (*bool*) – If set to False, the fields *SYMBOL_NAME* and *TICK_TYPE* are not appended to the output ticks.
  * **presort** (*bool*) – Add the **PRESORT** EP before merging.
    By default, it is set to True if `symbols` are set
    and to False otherwise.
  * **concurrency** (*int*) – 

    Specifies the number of CPU cores to utilize for the `presort`.
    By default, the value is inherited from the value of the query where this PRESORT is used.

    For the main query it may be specified in the `concurrency` parameter of ``run()`` method
    (which by default is set to
    ``otp.config.default_concurrency``).

    For the auxiliary queries (like first-stage queries) empty value means OneTick’s default of 1.
    If ``otp.config.presort_force_default_concurrency``
    is set then default concurrency value will be set in all PRESORT EPs in all queries.
  * **batch_size** (*int*) – Specifies the query batch size for the `presort`.
    By default, the value from
    ``otp.config.default_batch_size``
    is used.
  * **output_type_index** (*int*) – Specifies index of source in `sources` from which type and properties of output will be taken.
    Useful when merging sources that inherited from ``Source``.
    By default, output object type will be ``Source``.
  * **add_symbol_index** (*bool*) – If set to True, this function adds a field *SYMBOL_INDEX* to each tick,
    with a numeric index (1-based) corresponding to the symbol the tick is for.
  * **separate_db_name** (*bool*) – If set to True, the security name of the input time series is separated into
    the pure symbol name and the database name parts
    propagated in the *SYMBOL_NAME* and *DB_NAME* fields, respectively.
    Otherwise, the full symbol name is propagated in a single field called *SYMBOL_NAME*.
  * **added_field_name_suffix** (*str*) – The suffix to add to the names of additional fields
    (that is, *SYMBOL_NAME*, *TICK_TYPE*, *DB_NAME* and *SYMBOL_INDEX*).
  * **stabilize_schema** (*bool*) – 

    If set to True, any fields that were present on any tick in the input time series
    will be present in the ticks of the output time series.
    New fields will be added to the output tick at the point they are first seen in the input time series.
    If any field already present in the input is not present on a given input tick,
    its type will be determined by the widest encountered type under that field name.
    Incompatible types (for example, int and float) under the same field name will result in an exception.

    Default is False.
  * **enforce_order** (*bool*) – 

    If merged ticks have the same timestamp, their order is not guaranteed by default.
    Set this parameter to True to set the order according to parameter `sources`.

    Special OneTick field *OMDSEQ* will be used to order sources.
    If it exists then it will be overwritten and deleted.
  * **symbol_date** (``otp.datetime`` or ``datetime.datetime`` or int) – Symbol date or integer in the YYYYMMDD format.
    Can only be specified if parameters `symbols` is set.
* **Returns:**
  A time series of ticks.
* **Return type:**
  ``Source`` or same class as `sources[output_type_index]`

#### NOTE
If merged ticks have the same timestamp, their order is not guaranteed by default.
Set parameter `enforce_order` to set the order according to parameter `sources`.

##### Examples

`merge` is used to merge different data sources:

```
>>> data1 = otp.Ticks(X=[1, 2], Y=['a', 'd'])
>>> data2 = otp.Ticks(X=[-1, -2], Y=['*', '-'])
>>> data = otp.merge([data1, data2])
>>> otp.run(data)
                     Time  X  Y
0 2003-12-01 00:00:00.000  1  a
1 2003-12-01 00:00:00.000 -1  *
2 2003-12-01 00:00:00.001  2  d
3 2003-12-01 00:00:00.001 -2  -
```

Merge series from multiple symbols into one series:

```
>>> data = otp.Ticks(X=[1])
>>> data['SYMBOL_NAME'] = data['_SYMBOL_NAME']
>>> symbols = otp.Ticks(SYMBOL_NAME=['A', 'B'])
>>> data = otp.merge([data], symbols=symbols)
>>> otp.run(data)
        Time  X SYMBOL_NAME
0 2003-12-01  1           A
1 2003-12-01  1           B
```

Use `identify_input_ts` and other parameters to add information about symbol to each tick:

```
>>> symbols = otp.Ticks(SYMBOL_NAME=['COMMON::S1', 'DEMO_L1::S2'])
>>> data = otp.Tick(A=1, db=None, tick_type='TT')
>>> data = otp.merge([data], symbols=symbols, identify_input_ts=True,
...                  separate_db_name=True, add_symbol_index=True, added_field_name_suffix='__')
>>> otp.run(data)
        Time  A SYMBOL_NAME__ DB_NAME__ TICK_TYPE__  SYMBOL_INDEX__
0 2003-12-01  1            S1    COMMON          TT               1
1 2003-12-01  1            S2   DEMO_L1          TT               2
```

Adding symbol parameters before merge:

```
>>> symbols = otp.Ticks(SYMBOL_NAME=['S1', 'S2'], param=[1, -1])
>>> def func(symbol):
...     pre = otp.Ticks(X=[1])
...     pre["SYMBOL_NAME"] = symbol.name
...     pre["PARAM"] = symbol.param
...     return pre
>>> data = otp.merge([func], symbols=symbols)
>>> otp.run(data)[['PARAM', 'SYMBOL_NAME']]
   PARAM SYMBOL_NAME
0      1          S1
1     -1          S2
```

Use parameter `output_type_index` to specify which input class to use to create output object.
It may be useful in case some custom user class was used as input:

```
>>> class CustomTick(otp.Tick):
...     def custom_method(self):
...         return 'custom_result'
>>> data1 = otp.Tick(A=1)
>>> data2 = CustomTick(B=2)
>>> data = otp.merge([data1, data2], output_type_index=1)
>>> type(data)
<class 'onetick.py.functions.CustomTick'>
>>> data.custom_method()
'custom_result'
>>> otp.run(data)
        Time  A  B
0 2003-12-01  1  0
1 2003-12-01  0  2
```

##### SEE ALSO
**MERGE** and **PRESORT** OneTick event processors
