# otp.Source.join_with_snapshot

#### ``Source.join_with_snapshot(snapshot_name='VALUE', snapshot_storage='memory', allow_snapshot_absence=False, join_keys=None, symbol_name_in_snapshot=None, database='', default_fields_for_outer_join=None, prefix_for_output_ticks='', snapshot_fields=None)``

Saves last (at most) n ticks of each group of ticks from the input time series in global storage or
in a memory mapped file under a specified snapshot name.
Tick descriptor should be the same for all ticks saved into the snapshot.
These ticks can then be read via ``ReadSnapshot`` by using the name
of the snapshot and the same symbol name (`<db_name>::<symbol>`) that were used by this method.

#### WARNING
You should update schema manually, if you want to use fields from snapshot in onetick-py query description
before its execution.

That’s due to the fact, that onetick-py can’t identify a schema of data in a snapshot before making a query.

If you set `default_fields_for_outer_join` parameter, schema will be guessed from default fields values.

* **Parameters:**
  * **snapshot_name** (*str*) – 

    The name that was specified in ``onetick.py.Source.save_snapshot()`` as a `snapshot_name` during saving.

    Default: VALUE
  * **snapshot_storage** (*str*) – 

    This parameter specifies the place of storage of the snapshot. Possible options are:
    * memory - the snapshot is stored in the dynamic (heap) memory of the process
      that ran (or is still running) the ``onetick.py.Source.save_snapshot()`` for the snapshot.
    * memory_mapped_file - the snapshot is stored in a memory mapped file.
      For each symbol to get the location of the snapshot in the file system, `join_with_snapshot` looks at
      the **SAVE_SNAPSHOT_DIR** parameter value in the locator section for the database of the symbol.
      In a specified directory it creates a new directory with the name of the snapshot and keeps
      the memory mapped file and some other helper files there.

    Default: memory
  * **allow_snapshot_absence** (*bool*) – 

    If specified, the EP does not display an error about missing snapshot
    if the snapshot has not been saved or is still being saved.

    Default: False
  * **join_keys** (*list* *,* *optional*) – A list of names of attributes. A non-empty list causes input ticks to be joined only if all of them
    have matching values for all specified attributes.
    Currently, these fields need to match with `group_by` fields of the corresponding snapshot.
  * **symbol_name_in_snapshot** (str, ``Column`` or ``Operation``, optional) – Expression that evaluates to a string containing symbol name.
    Specified expression is reevaluated upon the arrival of each tick.
    If this parameter is empty, the input symbol name is used.
  * **database** (*str* *,* *optional*) – The database to read the snapshot. If not specified database from the symbol is used.
  * **default_fields_for_outer_join** (*dict* *,* *optional*) – 

    A dict with field name as key and value, ``Column`` or ``Operation``,
    which specifies the names and the values of the fields (also, optionally, the field type),
    used to form ticks to be joined with unmatched input ticks.

    If you want to specify field type, pass tuple of field dtype and expression or value as dict item value.

    This parameter is reevaluated upon the arrival of each tick.

    It’s also used for auto detecting snapshot schema for using fields from snapshot
    while building query via `ontick-py`.
  * **prefix_for_output_ticks** (*str*) – 

    The prefix for the names of joined tick fields.

    Default: empty string
  * **snapshot_fields** (*list* **[*str* *]* *,* *None*) – Specifies list of fields from the snapshot to join with input ticks. When empty, all fields are included.
  * **self** (*Source*)

##### Examples

Simple ticks join with snapshot:

```
>>> src = otp.Ticks(A=[1, 2])
>>> src = src.join_with_snapshot(snapshot_name='some_snapshot')  
>>> otp.run(src)  
                     Time  A  X  Y               TICK_TIME
0 2003-12-01 00:00:00.000  1  1  4 2003-12-01 00:00:00.000
1 2003-12-01 00:00:00.000  1  2  5 2003-12-01 00:00:00.001
2 2003-12-01 00:00:00.001  2  1  4 2003-12-01 00:00:00.000
3 2003-12-01 00:00:00.001  2  2  5 2003-12-01 00:00:00.001
```

Add prefix `T.` for fields from snapshot:

```
>>> src = otp.Ticks(A=[1, 2])
>>> src = src.join_with_snapshot(
...     snapshot_name='some_snapshot', prefix_for_output_ticks='T.',
... )  
>>> otp.run(src)  
                     Time  A  T.X  T.Y             T.TICK_TIME
0 2003-12-01 00:00:00.000  1    1    4 2003-12-01 00:00:00.000
1 2003-12-01 00:00:00.000  1    2    5 2003-12-01 00:00:00.001
2 2003-12-01 00:00:00.001  2    1    4 2003-12-01 00:00:00.000
3 2003-12-01 00:00:00.001  2    2    5 2003-12-01 00:00:00.001
```

To get only specific fields from snapshot use parameter `snapshot_fields`:

```
>>> src = otp.Ticks(A=[1, 2])
>>> src = src.join_with_snapshot(
...     snapshot_name='some_snapshot', snapshot_fields=['Y'],
... )  
>>> otp.run(src)  
                     Time  A  Y
0 2003-12-01 00:00:00.000  1  4
1 2003-12-01 00:00:00.000  1  5
2 2003-12-01 00:00:00.001  2  4
3 2003-12-01 00:00:00.001  2  5
```

Setting default values for snapshot fields for outer join via `default_fields_for_outer_join_with_types`
parameter with example of joining ticks with absent snapshot:

```
>>> src = otp.Ticks(A=[1, 2])
>>> src = src.join_with_snapshot(
...     snapshot_name='some_snapshot', allow_snapshot_absence=True,
...     default_fields_for_outer_join={
...         'B': 'Some string',
...         'C': (float, src['A'] * 2),
...         'D': 50,
...     },
... )  
>>> otp.run(src)  
                     Time  A            B    C     D
0 2003-12-01 00:00:00.000  1  Some string  2.0  50.0
1 2003-12-01 00:00:00.001  2  Some string  2.0  50.0
```

In this case, schema for `src` object will be automatically detected from values for this parameter:

```
>>> src.schema  
{'A': <class 'int'>, 'B': <class 'str'>, 'C': <class 'float'>, 'D': <class 'int'>}
```

You can join ticks from snapshot for each input tick for specified symbol name from string value or this tick
via `symbol_name_in_snapshot` parameter.

Let’s create snapshot with different symbol names inside:

```
>>> src = otp.Ticks(X=[1, 2, 3, 4], Y=['AAA', 'BBB', 'CCC', 'AAA'])
>>> src = src.save_snapshot(
...     snapshot_name='some_snapshot', num_ticks=5, keep_snapshot_after_query=True, symbol_name_field='Y',
... )
>>> otp.run(src)  
```

Now we can join input only with ticks from snapshot with specified symbol name:

```
>>> src = otp.Ticks(A=[1, 2])
>>> src = src.join_with_snapshot(
...     snapshot_name='some_snapshot', symbol_name_in_snapshot='AAA',
... )  
>>> otp.run(src)  
                     Time  A  X               TICK_TIME
0 2003-12-01 00:00:00.000  1  1 2003-12-01 00:00:00.000
1 2003-12-01 00:00:00.000  1  4 2003-12-01 00:00:00.003
2 2003-12-01 00:00:00.001  2  1 2003-12-01 00:00:00.000
3 2003-12-01 00:00:00.001  2  4 2003-12-01 00:00:00.003
```

Or we can join each tick with ticks from snapshot with symbol name from input ticks field:

```
>>> src = otp.Ticks(A=[1, 2], SYM=['AAA', 'CCC'])
>>> src = src.join_with_snapshot(
...     snapshot_name='some_snapshot', symbol_name_in_snapshot=src['SYM'],
... )  
>>> otp.run(src)  
                     Time  A  SYM  X               TICK_TIME
0 2003-12-01 00:00:00.000  1  AAA  1 2003-12-01 00:00:00.000
1 2003-12-01 00:00:00.000  1  AAA  4 2003-12-01 00:00:00.003
2 2003-12-01 00:00:00.001  2  CCC  3 2003-12-01 00:00:00.002
```

##### SEE ALSO
**JOIN_WITH_SNAPSHOT** OneTick event processor

``onetick.py.ReadSnapshot``

``onetick.py.ShowSnapshotList``

``onetick.py.FindSnapshotSymbols``

``onetick.py.Source.save_snapshot()``

