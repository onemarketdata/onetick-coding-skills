# otp.ShowSnapshotList

### ``class ShowSnapshotList(snapshot_storage='all', **kwargs)``

Bases: ``Source``

Outputs all snapshots names for global memory storage and/or for memory mapped files.
These snapshots should be written there by the ``onetick.py.Source.save_snapshot()``.
Output ticks have `SNAPSHOT_NAME`, `STORAGE_TYPE` and `DB_NAME` fields.

* **Parameters:**
  **snapshot_storage** ( *'memory'* *or*  *'memory_mapped_file'*) – 

  This parameter specifies the place of storage of the snapshot. Possible options are:
  * memory - the snapshot is stored in the dynamic (heap) memory of the process
    that ran (or is still running) the ``onetick.py.Source.save_snapshot()`` for the snapshot.
  * memory_mapped_file - the snapshot is stored in a memory mapped file.
    For each symbol to get the location of the snapshot in the file system, `ReadSnapshot` looks at
    the **SAVE_SNAPSHOT_DIR** parameter value in the locator section for the database of the symbol.
  * all - shows both, memory, memory_mapped_file snapshots.

  Default: all

##### Examples

List snapshots from all snapshot storage types:

```
>>> src = otp.ShowSnapshotList(snapshot_storage='all')  
>>> otp.run(src)  
        Time SNAPSHOT_NAME        STORAGE_TYPE        DB_NAME
0 2003-12-01    snapshot_1              MEMORY        DEMO_L1
1 2003-12-01    snapshot_2              MEMORY        DEMO_L1
2 2003-12-01    snapshot_3  MEMORY_MAPPED_FILE  SNAPSHOT_DEMO
```

List snapshots from memory:

```
>>> src = otp.ShowSnapshotList(snapshot_storage='memory')  
>>> otp.run(src)  
        Time SNAPSHOT_NAME        STORAGE_TYPE        DB_NAME
0 2003-12-01    snapshot_1              MEMORY        DEMO_L1
1 2003-12-01    snapshot_2              MEMORY        DEMO_L1
```

List snapshots from memory mapped files:

```
>>> src = otp.ShowSnapshotList(snapshot_storage='memory_mapped_file')  
>>> otp.run(src)  
        Time SNAPSHOT_NAME        STORAGE_TYPE        DB_NAME
0 2003-12-01    snapshot_3  MEMORY_MAPPED_FILE  SNAPSHOT_DEMO
```

##### SEE ALSO
**SHOW_SNAPSHOT_LIST** OneTick event processor

``onetick.py.ReadSnapshot``

``onetick.py.FindSnapshotSymbols``

``onetick.py.Source.save_snapshot()``

``onetick.py.Source.join_with_snapshot()``

