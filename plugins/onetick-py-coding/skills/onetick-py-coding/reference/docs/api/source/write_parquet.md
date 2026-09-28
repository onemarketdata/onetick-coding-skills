# otp.Source.write_parquet

#### ``Source.write_parquet(output_path, compression_type='snappy', num_tick_per_row_group=1000, partitioning_keys='', propagate_input_ticks=False, inplace=False)``

Writes the input tick series to parquet data file.

Input must not have field ‘time’ as that field will also be added by the EP in the resulting file(s)

* **Parameters:**
  * **output_path** (*str*) – Path for saving ticks to Parquet file.
    Partitioned: Path to the root directory of the parquet files.
    Non-partitioned: Path to the parquet file.
  * **compression_type** (*str*) – Compression type for parquet files.
    Should be one of these: gzip, lz4, none, snappy (default), zstd.
  * **num_tick_per_row_group** (*int*) – Number of rows per row group.
  * **partitioning_keys** (*list* *,* *str*) – 

    List of fields (list or comma-separated string) to be used as keys for partitioning.

    Setting this parameter will switch this EP to partitioned mode.

    In non-partitioned mode, if the path points to a file that already exists, it will be overridden.
    When partitioning is active:
    * The target directory must be empty
    * Key fields and their string values will be automatically URL-encoded to avoid conflicts with
      : filesystem naming rules.

    Pseudo-fields ‘_SYMBOL_NAME’ and ‘_TICK_TYPE’ may be used as partitioning_keys and
    will be added to the schema automatically.
  * **propagate_input_ticks** (*bool*) – Switches propagation of the ticks. If set to True, ticks will be propagated.
  * **inplace** (*bool*) – A flag controls whether operation should be applied inplace.
    If `inplace=True`, then it returns nothing. Otherwise method
    returns a new modified object.

##### Examples

Simple usage:

```
>>> data = otp.Ticks(A=[1, 2, 3])
>>> data = data.write_parquet("/path/to/parquet/file")  
>>> otp.run(data)  
```

##### SEE ALSO
**WRITE_TO_PARQUET** OneTick event processor

``onetick.py.ReadParquet``

