# otp.FindSnapshotSymbols

### ``class FindSnapshotSymbols(snapshot_name='VALUE', snapshot_storage='memory', pattern='%', symbology=None, show_original_symbols=False, discard_on_match=False, db=None, tick_type=None, query_parameters=None, **kwargs)``

Bases: ``Source``

Takes snapshot’s name and storage type and produces a union of all symbols in the snapshot.
It also optionally translates the symbols into a user-specified symbology and
propagates those that match a specified name pattern as a tick with a single field: **SYMBOL_NAME**.

If symbol name translation is performed, symbol names in native snapshot can optionally be propagated
as another tick field: `ORIGINAL_SYMBOL_NAME`, in which case symbols with missing translations
are also propagated. Symbol name translation is performed as of the query symbol date and requires
the reference database to be configured.

The name of the database to query is extracted from the input symbol.
For example, if an input symbol is `TAQ::`, the symbols from the **TAQ** database will be returned.

The `pattern` parameter can contain the special characters `%` (matches any number of characters) and
`_` (matches any character).
For example, the pattern `I_M%` returns any symbol beginning with I and has M as its third letter.

If the `discard_on_match` parameter is set to `True`, the names that do not match the pattern
will be propagated.

The timestamps of the ticks created by `FindSnapshotSymbols` are set to the start time of the query.

* **Parameters:**
  * **snapshot_name** (*str*) – 

    The name that was specified in ``onetick.py.Source.save_snapshot()`` as a `snapshot_name`
    during saving.

    Default: VALUE
  * **snapshot_storage** ( *'memory'* *or*  *'memory_mapped_file'*) – 

    This parameter specifies the place of storage of the snapshot. Possible options are:
    * memory - the snapshot is stored in the dynamic (heap) memory of the process
      that ran (or is still running) the ``onetick.py.Source.save_snapshot()`` for the snapshot.
    * memory_mapped_file - the snapshot is stored in a memory mapped file.
      For each symbol to get the location of the snapshot in the file system, `ReadSnapshot` looks at
      the **SAVE_SNAPSHOT_DIR** parameter value in the locator section for the database of the symbol.

    Default: memory
  * **pattern** (*str*) – 

    The pattern for symbol selection. It can contain special characters `%` (matches any number of characters)
    and `_` (matches any character). To avoid this special interpretation of characters `%` and `_`,
    the `\` character must be added in front of them. Note that if symbol name translation is required,
    translated rather than original symbol names are checked to match the name pattern.
    > Default: %
  * **symbology** (*Optional* **[*str* *]*) – The destination symbology for a symbol name translation, the latter being performed,
    if destination symbology is not empty and is different from that of the queried database.
  * **show_original_symbols** (*bool*) – 

    Switches original symbol name propagation as a tick field after symbol name translation is performed.
    Note that if this parameter is set to true, database symbols with missing translations are also propagated.

    Default: False
  * **discard_on_match** (*bool*) – 

    When set to true only ticks that did not match the filter are propagated,
    otherwise ticks that satisfy the filter condition are propagated.

    Default: False
  * **db** (*str* *,* *optional*) – 

    Database to use for snapshots search query.

    It’s required to fill either this parameter or explicitly specify
    the `symbols` parameter of ``otp.run``,
    or have bound symbols on any node of your query
  * **tick_type** (*str* *,* *optional*) – Tick type.
  * **query_parameters** (``otp.QueryParameters``) – Additional query properties to be set in the resulting .otq file.
    They will be used if they are not overridden by other parameters or in ``otp.run``.

##### Examples

Find symbols for snapshot some_snapshot in database S1:

```
>>> src = otp.FindSnapshotSymbols(snapshot_name='some_snapshot', db='S1')
>>> otp.run(src, symbols='S1::')  
        Time SYMBOL_NAME
0 2003-12-01    S1::AAPL
1 2003-12-01    S1::AAAA
2 2003-12-01    S1::MSFT
```

Use `pattern` parameter to filter symbol names:

```
>>> src = otp.FindSnapshotSymbols(snapshot_name='some_snapshot', db='S1', pattern='A%')
>>> otp.run(src, symbols='S1::')  
        Time SYMBOL_NAME
0 2003-12-01    S1::AAPL
1 2003-12-01    S1::AAAA
```

Select symbol names not matched by pattern:

```
>>> src = otp.FindSnapshotSymbols(
...     snapshot_name='some_snapshot', db='S1', pattern='A%', discard_on_match=True,
... )
>>> otp.run(src, symbols='S1::')  
        Time SYMBOL_NAME
0 2003-12-01    S1::MSFT
```

##### SEE ALSO
**FIND_SNAPSHOT_SYMBOLS** OneTick event processor

``onetick.py.ReadSnapshot``

``onetick.py.ShowSnapshotList``

``onetick.py.Source.save_snapshot()``

``onetick.py.Source.join_with_snapshot()``

