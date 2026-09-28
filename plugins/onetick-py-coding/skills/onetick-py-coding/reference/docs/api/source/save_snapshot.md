# otp.Source.save_snapshot

#### ``Source.save_snapshot(snapshot_name='VALUE', snapshot_storage='memory', default_db='CEP_SNAPSHOT', database='', symbol_name_field=None, expected_symbols_per_time_series=1000, num_ticks=1, reread_prevention_level=1, group_by=None, expected_groups_per_symbol=10, keep_snapshot_after_query=False, allow_concurrent_writers=False, remove_snapshot_upon_start=None, inplace=False)``

Saves last (at most) n ticks of each group of ticks from the input time series in global storage or
in a memory mapped file under a specified snapshot name.
Tick descriptor should be the same for all ticks saved into the snapshot.
These ticks can then be read via ``ReadSnapshot`` by using the name
of the snapshot and the same symbol name (`<db_name>::<symbol>`) that were used by this method.

The event processor cannot be used by default. To enable it, access control should be configured,
so user could have rights to use **SAVE_SNAPSHOT** EP.

* **Parameters:**
  * **snapshot_name** (*str*) – 

    The name of the snapshot, can be any string which doesn’t contain slashes or backslashes.
    Two snapshots can have the same name if they are stored in memory mapped files for different databases. Also,
    they can have the same names if they are stored in the memories of different processes (different tick_servers).
    In all other cases the names should be unique.

    Default: VALUE
  * **snapshot_storage** (*str*) – 

    This parameter specifies the place of storage of the snapshot. Possible options are:
    * memory - the snapshot is stored in the dynamic (heap) memory of the process
      that ran (or is still running) the ``onetick.py.Source.save_snapshot()`` for the snapshot.
    * memory_mapped_file - the snapshot is stored in a memory mapped file.
      For each symbol to get the location of the snapshot in the file system, `save_snapshot` looks at
      the **SAVE_SNAPSHOT_DIR** parameter value in the locator section for the database of the symbol.
      In a specified directory it creates a new directory with the name of the snapshot and keeps
      the memory mapped file and some other helper files there.

    Default: memory
  * **default_db** (*str*) – 

    The ticks with empty symbol names or symbol names with no database name as a prefix are saved as
    if they have symbol names equal to **DEFAULT_DB::SYMBOL_NAME** (where **SYMBOL_NAME** can be empty).
    These kinds of ticks, for example, can appear after merging time series. To save/read these ticks
    to/from storage a dummy database with the specified default name should be configured in the locator.

    Default: CEP_SNAPSHOT
  * **database** (*str* *,* *optional*) – Specifies the output database for saving the snapshot.
  * **symbol_name_field** (str, ``Column``, optional) – If this parameter is specified, then each input time series is assumed to be a union of several time series and
    the value of the specified attribute of each tick determines to which time series the tick actually belongs.
    These values should be pure symbol names (for instance if the tick belongs to the time series **DEMO_L1::A**,
    then the value of the corresponding attribute should be **A**) and the database name will be taken from
    symbol of the merged time series.
  * **expected_symbols_per_time_series** (*int*) – 

    This parameter makes sense only when `symbol_name_field` is specified.
    It is the number of real symbols that are expected to occur per input time series.
    Bigger numbers may result in larger memory utilization by the query but will make the query faster.

    Default: 1000
  * **num_ticks** (*int*) – 

    The number of ticks to be stored for each group per each symbol.

    Default: 1
  * **reread_prevention_level** (*int*) – 

    For better performance we do not use synchronization mechanisms between the snapshot writer[s] and reader[s].
    That is why when the writer submits ticks for some symbol very quickly the reader may fail to read
    those ticks, and it will keep trying to reread them until it succeeds.
    The `reread_prevention_level` parameter addresses this problem.
    The higher the reread prevention level the higher the chance for the reader to read ticks successfully.
    But high prevention level also means high memory utilization, that is why it is recommended to keep
    the value of this parameter unchanged until you get an error about inability of the reader to read the snapshot
    due to fast writer.

    Default: 1
  * **group_by** (list of str, ``Column``, optional) – When specified, the EP will keep the last **n** ticks of each group for each symbol;
    otherwise it will just keep the last **n** ticks of the input time series.
    The group is a list of input ticks with the same values in the specified fields.
  * **expected_groups_per_symbol** (*int*) – 

    The number of expected groups of ticks for each time series.
    The specified value is used only when `group_by` fields are specified,
    otherwise it is ignored, and we assume that the number of expected groups is 1.
    The number hints the EP to allocate memory for such number of tick groups each time
    a new group of ticks is going to be created and no free memory is left.

    Default: 10
  * **keep_snapshot_after_query** (*bool*) – 

    If the snapshot is saved in process memory and this parameter is set, the saved snapshot continues to live
    after the query ends. If this parameter is not set, the snapshot is removed as soon as the query finishes and
    its name is released for saving new snapshots with the same name.
    This parameter is ignored if the snapshot is saved in the memory mapped file.

    Default: False
  * **allow_concurrent_writers** (*bool*) – 

    If this parameter is `True` multiple saver queries can write to the same snapshot contemporaneously.
    But different writers should write to different time series.
    Also, saver queries should run inside the same process (i.e., different tick servers or loaders with otq
    transformers cannot write to the same `memory_mapped_file` snapshot concurrently).

    Default: False
  * **remove_snapshot_upon_start** (*bool* *,* *optional*) – 

    If this parameter is `True` the snapshot will be removed at the beginning of the query the next time
    `save_snapshot` is called for the same snapshot. If the parameter is `False` the snapshot
    with the specified name will be appended to upon the next run of `save_snapshot`.

    If you’ll leave this parameter as `None`, it will be equal to setting this parameter to `NOT_SET` in EP.
    `NOT_SET` option operates in the same way as `True` for `memory` snapshots or `False`
    for `memory_mapped_file` snapshots.

    Default: None (`NOT_SET`)
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object.
  * **self** (*Source*)

##### Examples

Save ticks to a snapshot in a memory:

```
>>> src = otp.Ticks(X=[1, 2, 3, 4, 5])
>>> src = src.save_snapshot(snapshot_name='some_snapshot')  
>>> otp.run(src)  
```

If you want to use snapshot, stored in memory, after query, use parameter `keep_snapshot_after_query`:

```
>>> src = src.save_snapshot(snapshot_name='some_snapshot', keep_snapshot_after_query=True)  
```

Snapshot will be associated with default database. You can set database via `database` parameter:

```
>>> src = src.save_snapshot(
...     snapshot_name='some_snapshot', database='SOME_DATABASE', keep_snapshot_after_query=True
... )  
>>> otp.run(src)  
>>> src = otp.ShowSnapshotList()  
>>> otp.run(src)  
        Time  SNAPSHOT_NAME STORAGE_TYPE        DB_NAME
0 2003-12-01  some_snapshot       MEMORY  SOME_DATABASE
```

By default, only one last tick per group, if it set, or from all ticks per symbol is saved.
You can change this number by setting `num_ticks` parameter:

```
>>> src = src.save_snapshot(snapshot_name='some_snapshot', num_ticks=100)  
```

Setting symbol name for every tick in snapshot from source field:

```
>>> src = otp.Ticks(X=[1, 2, 3], SYMBOL_FIELD=['A', 'B', 'C'])
>>> src = src.save_snapshot(
...     snapshot_name='some_snapshot', symbol_name_field='SYMBOL_FIELD', keep_snapshot_after_query=True,
... )  
>>> otp.run(src)  
>>> src = otp.FindSnapshotSymbols(snapshot_name='some_snapshot')  
>>> otp.run(src)  
        Time SYMBOL_NAME
0 2003-12-01  DEMO_L1::A
1 2003-12-01  DEMO_L1::B
2 2003-12-01  DEMO_L1::C
```

Group ticks by column `X` and keep last 2 ticks from each group:

```
>>> src = otp.Ticks(X=[0, 0, 0, 1, 1, 1], Y=[1, 2, 3, 4, 5, 6])
>>> src = src.save_snapshot(
...     snapshot_name='some_snapshot', group_by=[src['X']], num_ticks=2, keep_snapshot_after_query=True,
... )  
>>> otp.run(src)  
>>> src = otp.ReadSnapshot(snapshot_name='some_snapshot')  
>>> otp.run(src)  
        Time  X  Y               TICK_TIME
0 2003-12-01  0  2 2003-12-01 00:00:00.001
1 2003-12-01  0  3 2003-12-01 00:00:00.002
2 2003-12-01  1  5 2003-12-01 00:00:00.004
3 2003-12-01  1  6 2003-12-01 00:00:00.005
```

##### SEE ALSO
**SAVE_SNAPSHOT** OneTick event processor

``onetick.py.ReadSnapshot``

``onetick.py.ShowSnapshotList``

``onetick.py.FindSnapshotSymbols``

``onetick.py.Source.join_with_snapshot()``

