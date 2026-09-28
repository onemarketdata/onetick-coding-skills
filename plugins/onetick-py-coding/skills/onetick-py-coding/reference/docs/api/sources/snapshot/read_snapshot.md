# otp.ReadSnapshot

### ``class ReadSnapshot(snapshot_name='VALUE', snapshot_storage='memory', allow_snapshot_absence=False, symbol=utils.adaptive, db=utils.adaptive_to_default, tick_type=utils.adaptive, start=utils.adaptive, end=utils.adaptive, schema=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Reads ticks for a specified symbol name and snapshot name from global memory storage or
from a memory mapped file.

These ticks should be written there by the ``onetick.py.Source.save_snapshot()`` event processor.
Ticks with an empty symbol name (for example, those after merging the time series of different symbols)
are saved under **CEP_SNAPSHOT::** symbol name, so for reading such ticks a dummy database
with the name **CEP_SNAPSHOT** must be configured in the database locator file.

* **Parameters:**
  * **snapshot_name** (*str*) – 

    The name that was specified in ``onetick.py.Source.save_snapshot()`` as a `snapshot_name`
    during saving.

    Default: `VALUE`
  * **snapshot_storage** ( *'memory'* *or*  *'memory_mapped_file'*) – 

    This parameter specifies the place of storage of the snapshot. Possible options are:
    * memory - the snapshot is stored in the dynamic (heap) memory of the process
      that ran (or is still running) the ``onetick.py.Source.save_snapshot()`` for the snapshot.
    * memory_mapped_file - the snapshot is stored in a memory mapped file.
      For each symbol to get the location of the snapshot in the file system, `ReadSnapshot` looks at
      the **SAVE_SNAPSHOT_DIR** parameter value in the locator section for the database of the symbol.

    Default: memory
  * **allow_snapshot_absence** (*bool*) – 

    If specified, the EP does not display an error about missing snapshot
    if the snapshot has not been saved or is still being saved.

    Default: False
  * **symbol** (str, list of str, ``Source``, ``query``, ``eval query``) – Symbol(s) from which data should be taken.
  * **tick_type** (*str*) – Tick type.
    Default: ANY.
  * **start** (``otp.datetime``) – Start time for tick generation. By default the start time of the query will be used.
  * **end** (``otp.datetime``) – End time for tick generation. By default the end time of the query will be used.
  * **schema** (*dict*) – 

    Dictionary of columns names with their types.

    #### WARNING
    You should set schema manually, if you want to use fields in onetick-py query description
    before its execution.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Read snapshot from memory:

```
>>> src = otp.ReadSnapshot(snapshot_name='some_snapshot')
>>> otp.run(src)  
        Time  PRICE  SIZE               TICK_TIME
0 2003-12-01  100.2   500 2003-12-01 00:00:00.000
1 2003-12-01   98.3   250 2003-12-01 00:00:00.001
2 2003-12-01  102.5   400 2003-12-01 00:00:00.002
```

You can specify schema manually in order to reference snapshot fields while constructing query via onetick-py:

```
>>> src = otp.ReadSnapshot(
...     snapshot_name='some_snapshot', schema={'PRICE': float, 'SIZE': int},
... )
>>> src['VOLUME'] = src['PRICE'] * src['SIZE']
```

Read snapshot for specified database and symbol name:

```
>>> src = otp.ReadSnapshot(snapshot_name='some_snapshot', db='DB', symbol='AAA')
```

Read snapshot from memory mapped file:

```
>>> src = otp.ReadSnapshot(
...     snapshot_name='some_snapshot', snapshot_storage='memory_mapped_file', db='DB',
... )
```

##### SEE ALSO
**READ_SNAPSHOT** OneTick event processor

``onetick.py.ShowSnapshotList``

``onetick.py.FindSnapshotSymbols``

``onetick.py.Source.save_snapshot()``

``onetick.py.Source.join_with_snapshot()``

